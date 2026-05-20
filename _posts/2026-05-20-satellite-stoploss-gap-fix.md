---
layout: post
title: "stop loss가 있어도, 작동 안 하면 stop loss가 아니다 — 5시간 갭 사고"
date: 2026-05-20
categories: [investing, satellite]
tags: [satellite, stop-loss, kis-api, system-design, post-mortem]
---

> 5/19 진입한 KODEX 에너지화학이 -7.16% 손절로 마감.
> 누적 실현 PnL: +₩17K → +₩5K로 거의 다 까먹음.
> 진짜 문제는 손실 자체가 아니라, **stop loss가 작동 안 한 시점이 있었다는 것**.

---

## 사건 타임라인

### 5/19 (월) — 진입 + 첫 stop touch

- 09:01 KODEX 에너지화학 (XLE MEDIUM) 8주 매수 @ ₩16,760
- stop_price = ₩15,922 (-5%)
- target_exit = 5/22

5/19 OHLC: O=16,715 / H=16,760 / **L=15,800** / C=16,170

**일중 저점 ₩15,800이 stop 임계 ₩15,922를 *이미* 뚫었음.**

그런데 자동 손절 안 발동. 왜?

→ 우리 stop loss는 **14:00 cron 1회**만 가격 체크. 14:00 시점 가격은 stop 위였던 듯 (종가 ₩16,170인 거 보면 회복 구간). 그래서 cron이 "OK"로 판단하고 통과.

장이 끝난 후 시스템은 "오늘 stop 안 뚫었음" 으로 인식.

> 💡 **이게 문제의 핵심.**
> 진짜 stop loss는 가격이 임계를 *터치하는 순간* 발동해야 한다.
> 우리 시스템은 하루 1번 사진 찍어보고 판단했다.
> 사진 찍는 시점에 회복돼 있으면? 뚫었어도 못 잡음.

### 5/20 (화) — 추가 하락 + 발견

- 09:00 시가 ₩16,170
- 10시 즈음 일중 저점 ₩15,350 (-8.4%) 찍음
- 09:43 텔레그램으로 "포지션 마이너스 심한데?" 메시지 옴

그때 인지: **어제 stop touch 했는데 못 잡은 거구나.**

5/20 OHLC (10:23 시점): O=16,170 / **L=15,350** / 현재 ₩15,560.

이번엔 14:00까지 또 기다리면 추가 손실 누적. 시스템이 망가졌다는 게 명확해짐.

---

## 옵션 비교

진짜 stop loss는 **실시간**이어야 한다. KIS Open API의 한계:

| 옵션 | 갭 | 작업량 | 비고 |
|---|---|---|---|
| 14:00 단일 cron (구) | 5시간 | — | 사실상 stop 아님 |
| 매 1분 cron | 1분 | 15분 | 1분 사이 큰 폭락 시 못 잡음 |
| **KIS WebSocket** | ~1초 | 반나절 | 정답 |
| 거래소 조건부 매도 | 0초 | — | **KIS Open API 미지원** ❌ |

마지막 줄이 충격이었다. 한투 앱 UI엔 "스톱주문" 메뉴 있는데 Open API로는 노출 안 됨. 자동매매 시스템에서 "stop loss = 거래소 자동 트리거" 라고 가정한 게 처음부터 틀렸음.

직접 모니터링하고 직접 매도 fire 해야 함. → 결국 WebSocket이 답.

---

## Fix — KIS WebSocket 데몬

처음엔 매분 cron(중간 해결책)을 먼저 깔았다가 다시 갈아엎고 WebSocket으로 직행했다. 1분 갭도 갭이고 cron 2개 돌리는 게 cluttery.

```python
# satellite/realtime_stoploss.py (요약)
kis = PyKis(..., use_websocket=True)
stock = kis.stock(ticker)
ticket = stock.on("price", on_price_event)

def on_price_event(sender, e):
    price = int(e.response.price)
    if price <= stop_price:
        fire_market_sell(position)
```

launchd로 데몬화:
- `~/Library/LaunchAgents/com.satellite.realtime-stoploss.plist`
- `KeepAlive=true` (자동 재시작)
- `ThrottleInterval=30` (재시작 폭주 방지)
- `RunAtLoad=true` (부팅 시 시작)

`positions.json`을 10초마다 watch → 새 포지션 생기면 자동 구독, 만기/손절로 사라지면 unsubscribe. 매수 cron이 별도 프로세스라도 자동 동기화.

**Live test:**
- 삼성전자(005930) 구독 → 12초 동안 25틱 수신 ✓
- 가격 변동 latency ~500ms
- WebSocket 끊김 시 PyKis가 자동 reconnect + subscriptions 복원

`2026-05-19 stop touch` 같은 사고는 이제 1초 안에 잡힌다.

---

## 누적 손익 (4/30 ~ 5/20)

| 거래 | 종목 | 결과 |
|---|---|---|
| 4/30→5/7 | KODEX 반도체 1주 | 🟢 +₩17,180 (+13.77%) |
| 5/11→5/18 | KODEX 반도체 2주 | 🔴 -₩7,740 (-2.57%) |
| 5/11→5/18 | KODEX 로봇 7주 | 🟢 +₩5,845 (+2.16%) |
| 5/19→5/20 | KODEX 에너지화학 8주 | 🔴 -₩9,600 (-7.16%) |

**누적 실현: +₩5,685**

첫 거래 +₩17K가 사실상 전체를 끌고 있다. 13일 동안의 노력 대비 결과가 부족.

근데 백테 EV는 양수고 (71% 승률, +14.7만 / 12개월), 표본 4건은 너무 작다. 일희일비할 일은 아니다.

---

## 교훈 정리

1. **"stop loss 있음"과 "stop loss 작동함"은 다른 얘기.** 설계 시 시점·빈도·트리거 방식까지 명세 필요.

2. **외부 API의 한계는 직접 검증해야 함.** KIS Open API에 stop order 있을 거라 가정하고 시스템 설계 → 가정이 틀려서 5시간 갭.

3. **중간 해결책에 안주하지 말 것.** 매분 cron(1분 갭)으로 일단 깔까 했다가 결국 WebSocket으로 직행. 어차피 갈 곳이면 한 번에 가는 게 낫다.

4. **표본 4건으로 시스템 판단 X.** 백테 EV가 +면 단기 누적 손실은 노이즈. 단, 시스템 *결함*은 빨리 fix.

---

## 다음 step

- [x] ~~매분 cron stoploss~~ (intermediate, 결국 폐기)
- [x] KIS WebSocket 기반 실시간 stop loss (`realtime_stoploss.py` + launchd 데몬)
- [ ] 추후 표본 늘어나면 stop 임계 (-5%) 재검증

---

오늘 -₩9,600은 실수 비용이 아니라 시스템 결함 노출 비용이다. 결함 못 찾았으면 더 큰 손실로 다음에 알게 됐을 거. 발견해서 fix한 게 진짜 이득. 그래서 적어둔다.

---

## 후일담 — 같은 날 같은 종목 재매수

같은 날 15:25, 매수 cron이 시그널대로 또 KODEX 에너지화학을 9주 ₩15,520에 샀다. 오늘 아침 손절한 종목을 5시간 뒤에 다시 사는 거.

```
10:23 SELL 8주 @ ₩15,560 → 실현 -7.16%
15:25 BUY  9주 @ ₩15,520 → 새 stop ₩14,744
```

사람이라면 "방금 -7% 본 종목인데 다시 사는 게 맞나?" 망설였을 거다. 시스템은 망설이지 않는다. XLE MEDIUM 시그널이 살아있고, 같은 섹터 중복 체크는 매도 후 비어있어서 통과, 매수 룰대로 진입.

이게 룰 기반 시스템의 본질이다. 매번 직감으로 판단하면 결국 큰 손실 못 끊고 작은 익절만 챙기게 됨. 룰을 지키면 가끔 같은 종목에서 두 번 손해 볼 수도 있지만, 통계적으로 EV가 +.

WebSocket 데몬은 새 포지션도 자동 감지했다. 15:25:13에 `[subscribe] 117460` 로그가 떴고, 시장 마감(15:30) 후 자동 unsubscribe 됨. 월요일 09:00 시장 재개 시 자동 재구독 예정. 손절 갭은 더 이상 없다 — 적어도 장중에는.

> 진짜 무서운 건 손실 자체가 아니라, 손실이 *반복될 때 룰을 어기는 자기 자신*이다.
