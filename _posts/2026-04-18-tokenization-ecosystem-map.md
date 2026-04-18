---
layout: post
title: "토큰화 전체 지도 — 5개 영역과 돈의 흐름"
date: 2026-04-18
categories: [investing, crypto]
---

[지난 글](/investing/crypto/2026/04/18/tokenization-what-and-why.html)에서 토큰화가 뭔지 감을 잡았다. 이제 진짜 질문: **돈이 어디서 어디로 흐르는가?**

토큰화 생태계를 5개 영역으로 쪼개서 정리했다. 각 영역이 뭘 하는지, 누가 이기고 있는지, 그리고 서로 어떻게 연결되는지.

---

## 전체 구조: 5개 레이어

```
[5. 규제 인프라] ─── KYC/AML 게이트 ────────────────
     │                                              │
[4. L1 체인]     Ethereum · Solana · Avalanche       │
     │                                              │
[3. 결제/브릿지]  Ripple · Visa · SWIFT+Chainlink    │
     │                                              │
[2. 스테이블코인] USDT · USDC · RLUSD               │
     │                                              │
[1. RWA 프로토콜] Ondo · Securitize · Centrifuge     │
     └──────────── 돈의 흐름 (아래→위→아래) ─────────┘
```

아래에서 위로: 실물자산 → 토큰화 → 체인 위에 올라감 → 결제망 타고 이동
위에서 아래로: 기관 투자자 → 규제 통과 → 체인 접속 → RWA 토큰 매수

---

## 1. RWA 프로토콜 — 실물을 토큰으로 바꾸는 공장

### 국채 토큰화가 구체적으로 어떻게 작동하나?

실제 프로세스를 6단계로 정리하면:

1. **발행사가 미국채를 매수** — Ondo, BlackRock 등 라이선스 보유 기관이 실제 T-Bill을 산다
2. **규제 수탁사에 보관** — 매수한 국채를 규제 커스터디(예: BNY Mellon)에 분리 보관. 발행사 파산해도 자산 보호
3. **토큰 발행** — 풀(pool)에 묶인 국채를 기반으로 ERC-20 토큰 발행. 1토큰 = 풀 지분
4. **블록체인에 배포** — Ethereum, Solana 등 퍼블릭 체인에 스마트 컨트랙트 배포
5. **수익 분배** — 두 가지 모델: (a) 리베이싱 — 잔고 자동 증가, (b) 가격 상승 — 토큰 가치가 이자만큼 올라감
6. **상환** — 만기 시 법정화폐로 환매 또는 새 국채로 롤오버

### 시장 규모 (2026년 4월 기준)

| 자산 유형 | 온체인 규모 | YoY 성장 |
|-----------|------------|---------|
| 토큰화 국채 | ~$14B | +90% |
| 프라이빗 크레딧 | ~$3.2B | +180% |
| 토큰화 주식 | 폭발적 성장 | +2,800% |
| **RWA 전체** | **~$26.4B** | **+300%** |

### 주요 플레이어와 시장 점유율

**국채 토큰화 (Top 5가 시장의 ~69%)**

| 제품 | 운용사 | AUM | 특징 |
|------|--------|-----|------|
| BUIDL | BlackRock + Securitize | ~$2.5B | 최대 규모. 9개 체인 배포. Binance 담보로 사용 가능 |
| USDY | Ondo Finance | ~$1.9B | T-Bill 기반 수익형 토큰, APY ~3.5%. 16,500+ 홀더 |
| OUSG | Ondo Finance | (USDY 포함) | 단기 국채 펀드 토큰. 기관용 |
| USYC | Hashnote | 상위권 | |
| BENJI | Franklin Templeton | 상위권 | 전통 자산운용사 최초 진입 |

**프라이빗 크레딧**
- **Centrifuge** — 시장 리더. 실물 대출(무역금융, 부동산 등)을 온체인화
- **Maple Finance** — 기관 신용 풀 운영. "불투명한 사모대출 시장을 온체인으로" 비전. CEO가 "가장 큰 기회는 국채 토큰화가 아니라 프라이빗 크레딧"이라고 명언
- **Goldfinch** — 신흥국 대출 특화

**토큰화 주식 — Ondo Global Markets의 독주**
- 2025년 9월 런칭 이후 토큰화 주식 시장의 **50% 이상** 점유
- Tesla, NVIDIA, Apple 등 200+ 미국 주식/ETF를 토큰으로 발행
- 작동 방식: Alpaca(브로커-딜러)가 실제 주식 매수·수탁 → Ondo가 1:1 토큰 발행 → 배당·기업 이벤트 자동 반영
- 미국 외 투자자 대상 (아태, 유럽, 아프리카, 중남미)
- Abu Dhabi 규제 승인 획득 (2026년 3월)

**Securitize — "토큰화의 AWS"**
- SEC 등록 브로커-딜러 + 디지털 전환 대행사 + ATS 운영
- BlackRock, Apollo, KKR, Hamilton Lane, VanEck 등과 파트너
- 토큰화 자산 $4B+ 처리
- 2026년 상반기 SPAC 상장 예정 (밸류에이션 $1.25B)

---

## 2. 스테이블코인 — 토큰화의 기초 화폐

### 왜 스테이블코인이 토큰화의 기반인가?

간단하다. **토큰화된 국채를 사려면 온체인 달러가 필요하다.** USDC로 BUIDL을 사고, USDY로 이자를 받는다. 스테이블코인 없이는 RWA 토큰의 유동성 자체가 불가능.

### 시장 현황 (2026년 4월)

| 스테이블코인 | 발행사 | 시가총액 | 점유율 |
|-------------|--------|---------|-------|
| USDT | Tether | ~$184B | ~60% |
| USDC | Circle | ~$77B | ~25% |
| RLUSD | Ripple | ~$1.3B | <1% |
| 기타 (DAI, PYUSD 등) | 다수 | ~$38B | ~13% |
| **합계** | | **~$300B** | |

시장 전체가 **2030년 $3T 전망** — 현재의 10배.

### USDT vs USDC — 경쟁 구도

**USDT (Tether):** 여전히 압도적 1위. 하지만 준비금 투명성 논란이 계속. 규제 강화되면 불리.

**USDC (Circle):** 2년 연속 USDT보다 빠르게 성장. 규제 친화적 — 미국 달러 준비금 100% 공개.

**Circle IPO (티커: CRCL)**
- 2025년 6월 NYSE 상장, IPO 가격 $31
- 상장 직후 $200+ 급등 → 이후 $49.90까지 폭락 → 2026년 4월 현재 ~$107-123
- TTM 매출 $2.7B, 시총 ~$26B
- 아직 적자: 영업이익률 -3.5%, 순이익률 -2.5%
- 핵심 수익원: USDC 준비금의 이자 수익 (금리가 높을수록 유리)

### USDY — 스테이블코인과 RWA의 교차점

Ondo의 USDY는 "이자가 붙는 스테이블코인"이라고 보면 된다. 달러에 페깅되면서 T-Bill 수익(~3.5% APY)도 받는다. 기존 스테이블코인의 "보유 기회비용" 문제를 해결.

---

## 3. 결제·크로스보더 — 토큰이 국경을 넘는 통로

### Ripple/XRP — 은행들이 실제로 쓰고 있나?

**쓰고 있다. 하지만 복잡하다.**

- SWIFT 새 리테일 결제 프레임워크: 50+ 은행, 25+ 회랑, 2026년 중반 가동
- 그 중 **30개 은행이 Ripple 생태계와 연결** (Thunes 경유)
- HSBC, Standard Chartered, Bank of America, JPMorgan 등이 Ripple 기술 파일럿/통합
- **Deutsche Bank** — 2026년 초 Ripple 블록체인 + SWIFT 결합 크로스보더 결제 원장 구축

**핵심 구분:**
- ~60%는 **RippleNet 메시징만 사용** (XRP 불필요)
- ~40%는 **On-Demand Liquidity(ODL) 사용** → **XRP가 브릿지 자산으로 실제 사용됨**

Ripple CEO Brad Garlinghouse: "SWIFT와 파트너가 아니다. 경쟁자다. 대체할 것이다."

### Visa VTAP (Tokenized Asset Platform)

- 은행이 법정화폐 기반 토큰(스테이블코인, 토큰화 예금)을 발행·관리할 수 있게 해주는 플랫폼
- BBVA가 Ethereum 위에서 라이브 파일럿 진행
- Visa가 이미 PYUSD를 Ethereum, Solana, Stellar, Avalanche에서 지원

### PayPal PYUSD

- 2026년 3월: **70개국으로 확대**
- 가맹점이 PYUSD 결제 수락 시 **수분 내 정산** (기존 며칠 → 분 단위)
- 기존 결제 인프라 위에 스테이블코인 레이어를 얹는 전략

### SWIFT + Chainlink CCIP — 가장 주목할 통합

**이게 왜 중요한가:** SWIFT는 11,500개 은행을 연결하는 메시징 네트워크. Chainlink CCIP는 60+ 블록체인을 연결하는 크로스체인 프로토콜. **둘이 합치면 = 기존 금융 ↔ 블록체인 전체 연결.**

구체적 성과:
- 2025년 11월: SWIFT 회원 은행이 기존 인프라로 **블록체인 지갑 연결, 토큰화 자산 결제, 스마트 컨트랙트 실행** 가능해짐
- Project Guardian (싱가포르 MAS): UBS + SWIFT + Chainlink → 토큰화 펀드 청약/환매 결제 성공
- BNP Paribas, Intesa Sanpaolo, Societe Generale FORGE 등 유럽 대형 은행 참여
- CCIP 크로스체인 전송량: 2025년 **$7.77B** (전년 대비 +1,972%)
- CCIP가 보호하는 크로스체인 토큰 총 가치: **$33.6B**
- CCIP 2.0 (2026년 초): 기관이 보안/속도 트레이드오프를 직접 선택 가능

---

## 4. L1 체인 인프라 — 토큰이 살아가는 땅

### 왜 기관은 Ethereum을 선호하나?

**ERC-3643 = 규제 준수 토큰의 표준.**

- 토큰 자체에 KYC/AML 규칙이 내장됨 — "승인된 지갑만 전송 가능"
- SEC가 공식 연설에서 언급, DTCC가 ComposerX에 통합, MAS가 Project Guardian에 채택
- **$32B+ 자산이 ERC-3643으로 토큰화**, 180+ 관할권에서 사용
- 채택 기관: DTCC, Franklin Templeton, Apex Group 등

Ethereum의 기관 우위:
- 가장 큰 개발자 커뮤니티와 검증된 보안
- EVM 호환 체인으로 확장 가능 (Avalanche, Polygon, Arbitrum 등)
- BlackRock BUIDL이 Ethereum에서 최초 런칭 → 이후 8개 체인으로 확대

### Solana — 속도와 비용의 챌린저

- Ondo Global Markets가 200+ 토큰화 주식을 Solana에 배포 (2026년 1월)
- ERC-3643 호환 구현 완료 — 트랜잭션 하나에 토큰 명령 + 컴플라이언스 훅 포함
- 장점: 저비용, 고속도 → 리테일/고빈도 트레이딩에 유리
- 약점: 네트워크 안정성 논란, 기관 신뢰 Ethereum 대비 부족

### Avalanche — 기관 전용 체인의 개척자

**Evergreen Subnet (L1) 모델:**
- 기관 전용 **퍼미션드 체인**을 별도로 운영 가능
- **Spruce Subnet**: T. Rowe Price, WisdomTree, Wellington Management, Cumberland 참여
- KYC/KYB 통과한 지갑만 접속 — 비양도성 토큰(NTT)으로 신원 확인
- FX 스왑, 금리 스왑 등 실행 테스트 완료
- EVM 호환이므로 Ethereum 도구 그대로 사용

**Citi도 Spruce에서 프라이빗 마켓 토큰화 테스트 진행.**

### Chainlink — 체인들을 잇는 접착제

단순 오라클이 아니다. 토큰화 생태계의 **필수 인프라**:
- **가격 오라클**: RWA 토큰의 실시간 가격 피드 제공
- **CCIP**: 60+ 체인 간 토큰 이동 + SWIFT 연결
- **Proof of Reserve**: 토큰 뒷받침 자산의 실존 증명
- **자동화**: 이자 분배, 만기 상환 등 스마트 컨트랙트 자동 실행

Chainlink 없이는 "이 토큰이 진짜 국채로 뒷받침되는지", "다른 체인으로 보낼 수 있는지" 모두 불가능.

---

## 5. 규제 인프라 — 기관이 들어오는 문

### Fireblocks — 토큰화의 뒷단 전체를 깔아주는 회사

**뭘 하는 회사인가:**
- 디지털 자산 커스터디 (보관)
- 지갑 인프라 + 전송
- 토큰 발행 엔진 (Tokenization Engine 2.0 — 노코드로 35+ 체인에 배포)
- 글로벌 스테이블코인 결제 네트워크 (100+ 국가, 60+ 통화)

**규모:**
- 밸류에이션: $8B
- 고객: 2,400+ 기관 (80+ 은행 포함)
- 누적 보호 자산: **$10T+**

### Chainalysis — 블록체인 위의 경찰

**뭘 하는 회사인가:**
- 블록체인 트랜잭션 모니터링 & 분석
- KYT (Know Your Transaction) — 의심 거래 실시간 탐지
- 리스크 스코어링 — 지갑 주소별 위험도 평가
- 수사 기관 + 금융 기관에 데이터 제공

**Fireblocks + Chainalysis 통합:**
- Fireblocks에서 전송할 때 Chainalysis 리스크 스코어가 자동 적용
- 정책 위반 거래 자동 차단
- 기관이 "규정 준수하면서 온체인 활동" 가능하게 만드는 핵심

### 왜 이 레이어가 중요한가

2026년 기준, KYC/AML 컴플라이언스가 결정하는 것:
1. **기관 자금이 토큰화 딜에 들어올 수 있는지**
2. **토큰이 2차 시장에서 거래될 수 있는지**
3. **발행사가 규제 당국과 문제 없이 운영할 수 있는지**

규제 인프라 없이는 기관 어답션 자체가 불가능 → 이 레이어가 전체 생태계의 **게이트키퍼**.

---

## 돈의 흐름 — 5개 영역이 연결되는 방식

구체적 시나리오로 보자.

**"한국 기관투자자가 토큰화 미국채를 사는 과정":**

1. **규제 인프라(5)**: Fireblocks 커스터디 계정 개설, Chainalysis KYC 통과
2. **L1 체인(4)**: Ethereum 지갑 생성 (ERC-3643 호환)
3. **스테이블코인(2)**: 법정화폐 → USDC 전환 (Circle 경유)
4. **결제(3)**: SWIFT 메시지로 자금 이동, Chainlink CCIP로 체인 간 전송
5. **RWA 프로토콜(1)**: USDC로 BlackRock BUIDL 토큰 매수
6. **수익**: T-Bill 이자가 매일 토큰으로 분배

**역방향 — 이자 수익이 흐르는 경로:**

1. 미국 재무부 → 이자 지급
2. 커스터디(BNY Mellon) → 수탁 자산에 이자 반영
3. 스마트 컨트랙트 → 토큰 홀더에 비례 분배
4. 투자자 지갑 → USDC/USDY로 수령
5. 필요 시 → 법정화폐로 환전, 은행 계좌로 출금

---

## 어디에 베팅할 것인가? — Pick & Shovel 전략

골드러시 때 가장 돈을 번 건 금을 캔 사람이 아니라 **곡괭이와 삽을 판 사람**이다.

### 각 레이어별 투자 특성

| 레이어 | 예시 | 리스크 | 특징 |
|--------|------|--------|------|
| RWA 프로토콜 | ONDO, Centrifuge | 높음 | 어떤 프로토콜이 이길지 불확실. 승자독식 가능성 |
| 스테이블코인 | CRCL(Circle) | 중간 | 이미 상장. 금리 의존도 높음. 적자 중 |
| 결제 | XRP, LINK | 중-높 | 실제 채택과 토큰 가격의 괴리 큼 |
| L1 체인 | ETH, SOL, AVAX | 중간 | 토큰화 성장 = 체인 사용량 증가 = 수수료 수익 |
| 규제 인프라 | Fireblocks(비상장), Chainalysis(비상장) | 낮음 | 가장 안정적이지만 투자 접근성 제한 |

### 내 관점

**가장 "곡괭이" 성격이 강한 건:**
1. **Chainlink (LINK)** — 오라클 + CCIP + SWIFT 연결. 토큰화가 성장하면 무조건 수혜. 특정 RWA 프로토콜이 망해도 Chainlink는 필요
2. **Ethereum (ETH)** — ERC-3643 표준의 본산. 기관 디폴트 체인. 가스비 = 생태계 성장 수혜
3. **Circle (CRCL)** — USDC가 토큰화의 기축통화 역할. 리스크는 금리 하락과 적자 전환

RWA 프로토콜(Ondo 등)은 가장 직접적이지만 경쟁이 치열하고 승자를 예측하기 어렵다. Securitize 상장(2026 상반기) 이후 비교 분석이 필요.

---

## 다음 편 예고

이번 글에서 지도를 그렸으니, 다음에는 각 영역별로 더 깊이 파본다:
- RWA 프로토콜 심층 비교 (Ondo vs Securitize vs Centrifuge)
- 실제 투자 가능한 상품/토큰 정리
- 나의 토큰화 포트폴리오 설계

---

*참고 자료:*
- [RWA.xyz - Tokenized Real-World Asset Analytics](https://app.rwa.xyz/)
- [Chainlink CCIP](https://chain.link/cross-chain)
- [SWIFT + Chainlink Partnership](https://blog.chain.link/the-swift-and-chainlink-partnership/)
- [ERC-3643 공식](https://www.erc3643.org/)
- [BlackRock BUIDL - Securitize](https://securitize.io/blackrock/buidl)
- [Ondo Global Markets](https://finance.yahoo.com/news/ondo-global-markets-launches-100-024043617.html)
- [Circle (CRCL) Stock](https://stockanalysis.com/stocks/crcl/)
- [Fireblocks Tokenization](https://www.fireblocks.com/report/executive-tokenization-reading-list)
- [Avalanche Evergreen Subnets](https://www.avax.network/evergreen)
- [RedStone Tokenization & RWA Standards Report 2026](https://blog.redstone.finance/2026/03/26/tokenization-rwa-report-2026/)
- [Tokenized Equities +2,800% Growth](https://www.coindesk.com/business/2026/01/30/the-market-for-tokenized-equities-has-exploded-by-almost-3-000-in-a-single-year)
- [PayPal PYUSD 70개국 확대](https://www.coindesk.com/business/2026/03/17/paypal-expands-its-stablecoin-into-70-markets)
- [SWIFT 30 Ripple-Connected Banks](https://247wallst.com/investing/2026/04/01/xrp-news-swift-names-30-ripple-connected-banks-in-its-new-payment-framework/)
- [Deutsche Bank + Ripple](https://247wallst.com/investing/2026/02/24/deutsche-bank-adopts-ripples-rails-but-xrp-is-down-30-why-hasnt-xrp-moved/)
