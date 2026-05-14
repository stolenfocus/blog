---
layout: post
title: "NPS 미국주식 전략 전면 재검증 — 왜 Top20을 버렸나"
date: 2026-05-14
categories: [investing, nps-us]
tags: [backtest, validation, strategy]
---

> 2026-05-12 NPS Q1 2026 13F 공시 후 기존 전략 (Top20+6mo) 신뢰성 의심.
> 200+ 조합 백테스트 + 5년 롤링 + Out-of-Sample 검증 = 새로운 best practice 도출.

---

## 트리거 — "이 전략 진짜 맞나?"

2026-04-28 메모리에 "Top20+6mo+shares×price 평균 CAGR 15.77%, SPY 대비 +5.4%p" 기록해뒀었다. 4월에 이 전략으로 확정하기로 했었다.

근데 2026-05-12 Q1 2026 13F가 공시되고 다시 매수 직전, 문득 의심이 들었다.
> "정말 이 숫자 맞아? 200개 조합 다 안 보고 Top20에서 멈춘 거 아닌가?"

전면 재검증 시작.

---

## 발견된 3개 버그 (모두 수정)

### 1. signal_generator.py value-diff 버그 (CRITICAL)

LIVE 매매 시그널 생성 코드에서:

```python
# 옛날 (틀림)
dollar_invested = curr_value - prev_value

# 수정
dollar_invested = (curr_shares - prev_shares) × curr_price
```

NPS가 한 주도 안 샀는데 주가만 올라도 "많이 산 종목"으로 잡혔던 버그. 백테스트는 이미 fix돼 있었지만 LIVE 코드는 안 됨.

### 2. buy_price 분기 리셋 버그

`stress_test_top20_6mo.py:257-260`에서 분기마다 모든 종목 buy_price를 현재가로 덮어씀. 실현이익 추적 망가지고 세금 $0 잡힘 (실제 $15,448).

### 3. CUSIP 매핑 부실 (가장 큰 영향)

기존 `cusip_ticker_map.json`에 문제 다발:
- 외국 ticker 매핑 (DWD → 사실 MS, 4I1 → PM 등 12개)
- 회사명을 ticker로 입력 (AKAMAI TECHNOLOGIES INC → AKAM 35개)
- CINS prefix (G/N/Y) 미수록 (RPRX, AMCR 등)
- 일반 CUSIP 누락 (LRCX, BLK, GE, MRNA, SNAP 등)

OpenFIGI API + 수동 = **총 256개 매핑 추가**. 분기별 누락 비율:
- 2014-2017: 20-49% 누락 → 7-25%로 감소
- 2024-2026: 6-7% → 0~0.4%로 감소

**옛 분기들에서 NPS 매수의 30-50%가 백테스트에서 빠져 있었다**. 결과 ~7%p 부풀려져 있었음.

---

## 본격 검증 — 200+ 조합

### 1단계: Grid Search (TopN × N_BUY × selection × scoring)

총 200+ 조합 단일 11년 백테스트.

| Scoring | 결과 |
|---|---|
| shares×price | 1등 다수 |
| weight_delta | 2-3등 |
| new_only | 약함 (44% 승률) |
| value_pct | 약함 |

### 2단계: 5년 롤링 (27 윈도우)

Single 11년 우승자가 매번 우승하는지 확인. 100% SPY 승률 + 평균 알파 비교.

| 전략 | 승률 | 평균 CAGR | 평균 알파 | 최악 알파 |
|---|---|---|---|---|
| Top30 N=3 cheap | 92.6% | 31.74% | +19.71p | -2.99p ⚠️ |
| Top30 N=5 cheap | 96.3% | 31.60% | +19.57p | -0.59p |
| **Top30 N=6 cheap** | **100%** | **29.34%** | **+17.31p** | **+2.68p** ✅ |
| Top30 N=8-12 cheap | 100% | 23-25% | +12-13p | +0.2~1.1p |
| Top20 N=6 cheap | 100% | 23.35% | +11.32p | +1.79p |
| Top15 N=6 cheap | 100% | 21.42% | +9.39p | +1.61p |
| Top15 ALL (옛 추천) | 100% | 19.31% | +7.28p | +1.13p |

→ **Top30 + N=6 cheapest가 sweet spot**:
- N=5는 변동성 너무 큼 (한 윈도우 짐)
- N=8+는 알파 떨어짐 (희석)
- N=6에서 100% 승률 유지하며 최고 알파

### 3단계: Out-of-Sample Test

데이터 분할:
- TRAIN: 2014-09 ~ 2019-12
- TEST: 2020-03 ~ 2026-03 (모르고 결정)

TRAIN 우승자를 TEST에 적용:

| 전략 | TRAIN | TEST | 결과 |
|---|---|---|---|
| Top10 N=6 cheap | +17.08% | +25.46% (α +11.57p) | 🟢 통과 |
| Top30 N=6 cheap (추천) | +11.82% | +37.91% (α +24.02p) | 🟢 통과 |

5개 TRAIN 우승 후보 모두 TEST 통과. **Overfitting 아닌 진짜 신호**.

---

## 핵심 인사이트

### "비싼 종목 vs 싼 종목" — Cheapest이 이긴다

직관적으로는 NPS가 가장 많이 산 종목 (top_dollar = AAPL/NVDA/MSFT 같은 메가테크) 따라가는 게 맞아 보임. 하지만:

```
dollar_invested = shares_added × 가격
```

같은 100주를 사도:
- AAPL $300 × 100 = $30,000
- EQT $55 × 100 = $5,500

**top_dollar 정렬은 NPS conviction이 아니라 "가격에 의한 가중치"였다**.

cheapest 필터는 이 가격 가중치를 제거 → 순수 NPS conviction 추출. + 학계 검증된 size factor (소형주 효과) 활용.

### 메가테크 의존 사라짐

옛 Top15+6mo 픽: AAPL/NVDA/MSFT/AMZN/GOOGL/AVGO/TSLA/GEV/PANW/GOOG/LLY/JNJ (메가테크 30%)
신 Top30 N=6 cheapest 픽: **메가테크 0개**. 섹터 5개 분산.

메가테크 cycle이 끝나도 충격 X. 더 robust.

### 최대 낙폭 -53%

총 수익 +1,640% (16배)지만 중간에 -53% 갔던 적 있음 (코로나 2020-03). **5년 이상 보유**가 절대 조건. 단기 매수면 위험.

5년 이상 보유 시:
- 최악 5y 수익: +14.37%
- 평균 5y 수익: +258% (3.6배)
- 최고 5y 수익: +965% (10배)
- **27 윈도우 모두 양수**

---

## 결론

옛 Top20+6mo는 "조사 부족 + CUSIP 버그"가 만든 잘못된 결론이었다.

진짜 best practice는 **Top30 + 6mo 모멘텀 + N=6 cheapest + shares×price scoring**.

- 평균 CAGR: +29.3%
- SPY 대비 알파: +17.3%p  
- 5년 롤링 SPY 승률: 100% (27/27)
- Out-of-sample TEST 통과

다음 글에서 실제 매수 결과 공유.

---

*참고:*
- 검증 코드: [github.com/.../nps-us](https://github.com/) (private)
- 데이터: NPS 13F 2014-09 ~ 2026-03 (47 분기), CUSIP 935개 매핑, daily prices via yfinance
