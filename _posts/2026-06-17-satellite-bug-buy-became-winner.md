---
layout: post
title: "위성 — 버그가 만든 매수가 +16.64% 익절로 끝났다"
date: 2026-06-17
categories: [investing, satellite]
tags: [satellite, trade, post-mortem, dry-run-bug]
---

> [지난 글](/blog/investing/satellite/2026/06/10/satellite-stale-signal-bug-pause.html)에서 보고한 stale signal 버그.
> 6/10 NONE 스캔인데도 어제 시그널로 잘못 매수된 091160 1주.
> 7일 hold 후 +16.64% 익절. 5주 운용 첫 실질 흑전 +₩20,680.
> 거기 더해 dry-run state mutation 버그 두 번째도 발견.

---

## 1. 거래 결과

| 일자 | 액션 | 가격 | 비고 |
|------|------|------|------|
| 6/10 | BUY KODEX 반도체 1주 | ₩150,850 | **stale signal 버그로 매수** |
| 6/17 | SELL 1주 | **₩175,955** | 만기 매도 (수동, 지정가) |

**+₩25,105 (+16.64%)**

5주 누적 손익: -₩4,425 → **+₩20,680** (첫 흑전)

---

## 2. 매도 절차

LIVE_MODE = False라서 cron의 15:20 sell 액션은 dry run으로만 돔 (실제 매도 X).
실제 매도하려면 수동 KIS 호출:

```python
from pykis import PyKis
kis = PyKis(id="kr_isa", ...)
s = kis.stock('091160')
# 종가 단일가 매매 시간(15:20~15:30) 호환
# 시장가(ORD_DVSN=01)는 단일가에 안 먹음 — 지정가로
limit = (int(s.quote().price * 0.99) // 10) * 10  # 현재가 -1%, 10원 단위
order = s.sell(qty=1, price=limit)
```

15:30 단일가 매매에 ₩175,955로 체결. 지정가 ₩173,990 대비 +₩1,965 better fill.

5/28에 같은 패턴으로 ₩158K 지정가 → ₩160,810 체결한 케이스가 있어서 이번에도 동일하게 처리.

---

## 3. 아이러니: 버그가 만든 매수가 winner

원래 시나리오:
- 6/10 07:00 scan = NONE (US 어제 다 빠짐)
- 6/10 buy 크론은 매수 안 해야 함
- 근데 `load_latest_signal()` 버그 → 어제 6/9 signal 들고 와서 매수

만약 버그가 없었다면? → 매수 안 함 → 이 +16.64% 익절도 없었음.

물론 같은 버그로 그 전에 손절 5번 났던 이력이 있으니, 평균적으론 버그가 손해. 이번 한 번은 운이 좋았던 outlier.

그래도 흥미로운 데이터 포인트: 시그널이 폭락 다음날 reset되면서 "depressed price"에 매수된 게 평균회귀 + US 반등을 동시에 잡았다. 통계적으로 mean reversion이 효과 있다는 [이전 백테](/blog/investing/satellite/2026/06/12/...) 결과와 일관됨 (정식 글 아직).

---

## 4. 두 번째 버그: DRY RUN인데 state mutation

6/16에 sell 알림이 떴는데, "117460 KODEX 에너지화학 10주 매도" 라고 떴음.
근데 KIS 잔고엔 117460 없음. 어디서 나온 거?

추적: `auto_trader.py:322-324`
```python
positions.append(position)
save_positions(positions)  # LIVE_MODE 체크 없이 무조건 저장
```

KIS API 호출은 LIVE_MODE 체크 후 dry run하지만, **positions.json 저장은 무조건** 실행됨.

→ LIVE_MODE = False 상태에서도 buy 크론이 매일 fake 포지션을 positions.json에 쌓아왔음
→ 만기되면 sell 크론이 fake 매도 시뮬레이션
→ realtime_stoploss 데몬도 fake 포지션 추적하느라 WebSocket subscribe 낭비

오늘 091160 진짜 매도 후 positions.json 확인했더니 또 phantom 445290 7주가 들어가 있었음 (어제 6/16 dry-run BUY). 빈 배열로 reset.

수정 필요 (다음 작업):
```python
positions.append(position)
if config.LIVE_MODE:  # ← 추가
    save_positions(positions)
```

trade_log.csv 저장도 비슷하게 가드 필요.

---

## 5. 현재 상태 (2026-06-17 기준)

- LIVE_MODE = False 유지
- positions.json = []
- KIS 실제 잔고: 091160 0주, cash ₩57,658
- realtime_stoploss 데몬: subscribe 종목 없음 (idle)

남은 7/1 분기 review 작업:
1. ✅ `load_latest_signal()` fix (6/10)
2. **TODO**: dry-run state mutation 가드 (오늘 fix할 예정)
3. **TODO**: 매핑 fix (ICLN→신재생, XLE/XLY drop?)
4. **TODO**: 룰 변경 (1.5% 임계, 7d hold, top 1~2)
5. **TODO**: 백테 스크립트 정식 커밋

---

## 6. 배운 것

1. **버그라도 결과가 좋으면 즐기되 일반화 금물.** 1번의 행운이 시스템 신뢰를 만들진 않음. 평균을 봐야 함.
2. **DRY RUN은 진짜 "no-op" 이어야 한다.** 로그만 찍는 척하면서 상태를 건드리면 추적 불가능한 phantom 만듦. 모든 mutation에 LIVE_MODE 가드 필요.
3. **모니터링은 KIS 잔고 cross-reference로.** positions.json만 봤으면 phantom 못 찾았음. 실제 계좌와 매주 대조하는 routine 필요.
4. **종가 단일가 매도는 지정가로.** 시장가(ORD_DVSN=01)는 단일가 시간대에 미체결됨 (5/28 사고 후 학습). 1% 아래 지정가로 안전하게.

---

*다음 글: dry-run 가드 fix + 7/1 분기 review 종합. 위성 재시작 결정 포함 예정.*
