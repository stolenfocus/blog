---
layout: post
title: "위성 전략 일시 정지 — Stale Signal 버그 발견, 5월부터 잘못 사고 있었다"
date: 2026-06-10
categories: [investing, satellite]
tags: [satellite, bug, post-mortem, kis, pause]
---

> 5연속 손절. 이상해서 코드를 다시 봤다.
> `load_latest_signal()`이 날짜 검증 0%로 "그냥 가장 마지막 signal 파일"을 들고 와서 매수.
> 시그널 NONE인 날에도 어제/그저께/4일 전 signal로 매수해왔다.
> 발견 즉시 LIVE_MODE = False, 7/1 분기 review까지 정지.

---

## 1. 사건 타임라인

5/20~6/10 위성 매매 결과:

| 일자 | 종목 | 결과 | 손익 |
|------|------|------|------|
| 5/20 | KODEX 에너지화학 | stoploss | -₩9,600 |
| 5/28 | KODEX 로봇액티브 | stoploss | -₩14,035 |
| 6/2 | KODEX 2차전지 | stoploss | -₩6,825 |
| 6/5 | KODEX 반도체 | stoploss | -₩9,400 |
| 6/10 | KODEX 반도체 | stoploss | -₩7,950 |

**5연속 손절, 누적 -₩47,810.** 누적 손익 +₩4,975 → **-₩4,425**로 fade.

"매핑이 약해서 그런가" 의심 → 27개 KR ETF 후보 백테 → ICLN→2차전지 동전던지기 확인 ([6/2 글](/category/satellite/) 참고).
매핑 fix는 결정했지만 7/1까지 기다리는 중이었음. 그러는 사이 6/10에 또 손절. 그리고 같은 날 다시 매수.

**"왜 또 사?"** ← 이 질문이 버그 발견의 시작.

---

## 2. 버그 코드

`satellite/auto_trader.py:473-479` (수정 전):

```python
def load_latest_signal() -> dict | None:
    """Load the most recent signal JSON from signals/."""
    signal_files = sorted(config.SIGNALS_DIR.glob("signal_*.json"))
    if not signal_files:
        return None
    latest = signal_files[-1]  # 무조건 마지막 파일
    print(f"Loading signal: {latest.name}")
    with open(latest, encoding="utf-8") as f:
        return json.load(f)
```

**문제: 날짜 검증 없음.**

원래 의도한 흐름:
1. 매일 07:00 scan → US ETF 봄
2. STRONG/MEDIUM이면 `signal_YYYYMMDD_HHMMSS.json` 파일 생성
3. NONE이면 파일 생성 안 함
4. 15:25 buy 크론이 오늘 signal 파일 로드 → 매수
5. 파일 없으면 (NONE 날) → 매수 안 함

실제 동작:
- step 5에서 "파일 없으면" 체크 대신 **"가장 최근 파일이라도 들고 와서 매수"**
- 일주일 묵은 signal이라도 들고 옴

---

## 3. 사후 검증 (cross-reference)

5/20~6/10의 scan 결과 vs buy_loaded signal:

| 일자 | scan | buy가 로드한 signal | 비고 |
|------|------|---------------------|------|
| 5/28 | WEAK | 5/27 (1일 전) | stale, cash로 fail |
| 6/1 | NONE | 5/29 (**3일 전**) | stale, fail |
| 6/5 | NONE | 6/4 (어제) | stale, fail (cash) |
| 6/8 | NONE | 6/4 (**4일 전!**) | stale, fail (cash) |
| 6/10 | NONE | 6/9 (어제) | stale, **실제 BUY 됨** |

→ 5월 말부터 NONE 날마다 stale signal로 매수 시도해왔음. 대부분 cash 부족으로 우연히 막힘. 6/10은 cash 있어서 실제 체결.

---

## 4. 손절 5연속에 버그가 얼마나 기여했나

5건 stoploss 중 stale signal 영향 가능성:

| 일자 | 종목 | 그날 scan | 그날 buy_loaded | stale 여부 |
|------|------|----------|----------------|-----------|
| 5/20 | 에너지화학 | (조사 필요) | — | — |
| 5/28 | 로봇액티브 | 손절은 5/28, 매수는 5/27 (실시간 손절) | 5/27 매수 | OK |
| 6/2 | 2차전지 | 매수는 5/29 | 5/29 signal | OK |
| 6/5 | 반도체 | 매수는 6/4 | 6/4 signal | OK |
| 6/10 | 반도체 | 매수는 6/9 (신규는 오늘 stale 6/9 재매수) | 6/9 signal | 6/10 신규 매수는 **stale** |

직접적으로 stale로 인한 매수가 손절된 케이스는 6/10. 나머지는 정상 시그널이었지만 약한 매핑(XLE/ICLN/ROBO partial) + 종가매수 타이밍 손해가 누적.

근데 6/10 사건은 명백히 **버그가 손실 만든 케이스**. 시스템 신뢰 무너지는 트리거.

---

## 5. 픽스

```python
def load_latest_signal() -> dict | None:
    """Load TODAY'S signal JSON. Returns None if today's scan produced no signal."""
    from datetime import datetime
    today = datetime.now().strftime("%Y%m%d")
    today_files = sorted(config.SIGNALS_DIR.glob(f"signal_{today}_*.json"))
    if not today_files:
        print(f"No signal file for today ({today}). Scan returned NONE — skipping buy.")
        return None
    latest = today_files[-1]
    print(f"Loading signal: {latest.name}")
    with open(latest, encoding="utf-8") as f:
        return json.load(f)
```

검증:
```bash
$ python3 -c "from satellite.auto_trader import load_latest_signal; print(load_latest_signal())"
No signal file for today (20260610). Scan returned NONE — skipping buy.
None
```

오늘(NONE 스캔) 호출하면 None 리턴. 매수 안 함. ✅

---

## 6. 즉시 조치

`satellite/config.py:16`

```python
LIVE_MODE = False  # PAUSED 2026-06-10: stale signal bug found, 7/1 review까지 정지
```

- buy/sell 크론은 LIVE 체크 후 실행 안 함
- 보유 091160 1주는 그대로 (stop ₩143,307 / 만기 6/17)
- realtime_stoploss 데몬은 계속 작동 (보유 포지션 보호)

---

## 7. 7/1 분기 review 때 종합 fix 목록

이번 사건 + 그동안 발견한 매핑 이슈를 묶어서 분기 게이트에 한번에 적용:

1. ✅ `load_latest_signal()` fix (이미 적용)
2. 매핑 변경: ICLN→KODEX 2차전지 → **TIGER 신재생(377990)**
3. 약한 매핑 drop 검토: XLE (석유화학 sector mismatch), XLY (자동차 narrow)
4. 보유기간 룰: 5d/3d → 7d~10d (backtest +50~100% 개선)
5. 시그널 임계: 1% → 1.5% (선별성 ↑)
6. 백테 스크립트 정식 커밋 (현재 ad-hoc → satellite/backtest_*.py)

---

## 8. 오늘 배운 것

1. **"무조건 최신"은 검증이 아님.** 파일 시스템에 뭐가 남아있어도, 그게 오늘 의도한 결과라는 보장 없음. 항상 timestamp/date 검증해야 함.

2. **사일런트 버그가 가장 비싸다.** stale signal은 에러를 안 냈음. 그냥 "어제 결과로 오늘 매매"라는 명백한 잘못을 조용히 했음. 5월부터 6/10까지 약 3주간 비밀스럽게 활성.

3. **시스템 결과가 이상하면 코드 다시 봐라.** 손절 5연속 = 매핑이 약한 줄로만 생각했음. 실제론 매핑 + 버그 콜라보. 한 가지 원인으로 다 설명되지 않을 때 더 깊이 봐야 함.

4. **PAUSE 버튼은 자랑이 아니라 책임이다.** 의심스러우면 멈추기. 7/1까지 손 떼고 fix → 검증 → 재개. 손실 줄이는 가장 빠른 길.

5. **자동화 = "잘 만들면 돈 번다"가 아니라 "잘 만들고 + 잘 모니터링하면 돈 번다".** 모니터링 layer가 부실하면 자동화는 자동 손실. 시그널 vs 매수 cross-reference 같은 검증을 주기적으로 돌릴 dashboard 필요.

---

*이 글이 위성 전략의 "잠시 멈춤" 기록. 7/1 분기 review 글에서 fix + 백테 + 재개 결과 정리 예정.*
