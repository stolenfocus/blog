# 블로그 카테고리/태그 스키마

새 글 쓸 때 프론트매터를 이 규칙대로 채우면 알맞은 카테고리 페이지에 자동 노출됨.

## 카테고리 (categories)

복수 가능. 각 카테고리는 `/category/<name>/` 페이지를 가짐.

| 카테고리 | 페이지 | 목적 |
|---|---|---|
| `investing` | (없음) | 모든 투자 글에 공통으로 붙임 |
| `etf` | /category/etf/ | 부모님 연금저축 6 ETF 전략 |
| `dividend` | /category/dividend/ | ISA 배당 포트폴리오 |
| `pension` | /category/pension/ | 연금저축/IRP 관련 |
| `nps-us` | /category/nps-us/ | NPS 미국주식 추종 |
| `satellite` | /category/satellite/ | 위성 자동매매 전략 |
| `crypto` | /category/crypto/ | 코인 (BTC DCA, 토큰화) |

여러 페이지에 노출하고 싶으면 categories에 여러 개 적기. 예) `categories: [investing, etf, dividend]` → ETF + 배당 페이지 모두 노출.

## 태그 (tags)

카테고리 페이지 내 섹션 필터링용. 카테고리 페이지가 어떤 태그를 어떤 섹션에 노출시키는지:

### /category/etf/, /category/dividend/
| 섹션 | 필터 tag |
|---|---|
| 전략 | `strategy` |
| 월간 DCA | `dca` |
| 매수/매도 기록 (dividend만) | `trade` |
| 주간 리포트 | `weekly` |
| 기타 / 전체 글 | 위 태그 없는 카테고리 글 (fallback) |

### /category/crypto/
| 섹션 | 필터 tag |
|---|---|
| 토큰화 시리즈 | `tokenization` |
| BTC 가변 DCA | `btc-dca` |
| 기타 / 전체 글 | 위 태그 없는 카테고리 글 (fallback) |

### /category/pension/, /category/nps-us/, /category/satellite/
필터 없음. 카테고리에 속한 모든 글 자동 노출.

## 예시

**주간 자동 생성 글 (cron이 만듦):**
```yaml
categories: [investing, etf]
tags: [weekly]
```
→ /category/etf/ 의 "주간 리포트" 섹션

**월 DCA 글 (3계좌 — ETF + 배당 둘 다 다룸):**
```yaml
categories: [investing, etf, dividend]
tags: [monthly, parents, isa, dividend, etf, dca]
```
→ ETF + 배당 페이지 둘 다의 "월간 DCA" 섹션

**위성 전략 분석 글:**
```yaml
categories: [investing, satellite]
tags: [satellite, backtest, mapping]
```
→ /category/satellite/ 에 노출

## Fallback 메커니즘

ETF/배당/코인 페이지엔 "기타 / 전체 글" 섹션이 있음. 알려진 섹션 태그(`strategy`, `dca`, `weekly` 등)가 하나도 없는 글은 여기 자동 노출. 즉 카테고리만 맞으면 어딘가엔 무조건 보임.

## 새 카테고리 추가 시

1. `category/<new>.md` 만들고 `permalink: /category/<new>/`
2. `_config.yml`의 `header_pages`에 추가
3. 이 문서에 표 추가
