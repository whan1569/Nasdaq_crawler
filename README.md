# NASDAQ 데이터 크롤링 프로젝트

NASDAQ 상장 종목/ETF 티커와 주요 미국 지수 데이터를 수집하고, yfinance 기반 가격 데이터를 CSV로 저장한 뒤 HTML 차트 뷰어로 확인하기 위한 Python 기반 데이터 수집 도구입니다.

수집 당시 기준 약 3,900개 이상의 NASDAQ 티커를 확보했으며, 개별 종목 가격 데이터, 주요 지수 데이터, 섹터 ETF 데이터를 기간과 봉 단위별로 수집할 수 있도록 구성했습니다.

현재는 실시간 운용 중인 서비스가 아니라, 미국 주식 데이터 수집·저장·시각화 구조를 검증한 뒤 보관 중인 완성 후 사용 보류 프로젝트입니다.

## 📋 목차

- [기능](#기능)
- [설치 및 설정](#설치-및-설정)
- [사용법](#사용법)
- [데이터 저장 형식](#데이터-저장-형식)
- [크롤링 요령](#크롤링-요령)
- [파일 구조](#파일-구조)
- [데이터 활용 예시](#데이터-활용-예시)
- [주의사항](#주의사항)
- [현재 상태](#현재-상태)

---

## 기능

### 1. NASDAQ 티커 목록 수집

- NASDAQ screener API를 통해 NASDAQ 상장 티커 목록 수집
- 수집 결과를 `nasdaq_tickers.csv`로 저장
- 수집 당시 기준 약 3,900개 이상의 티커 확보

### 2. 개별 종목 가격 데이터 수집

- `nasdaq_tickers.csv`의 티커를 순회하며 개별 종목 가격 데이터 수집
- yfinance 기반 OHLCV 데이터 수집
- 사용자가 입력한 기간과 봉 단위에 따라 CSV 저장
- 예: `max`, `1y`, `6mo`, `30d` / `1m`, `5m`, `15m`, `1h`, `1d`
- 네트워크 오류나 데이터 누락에 대한 재시도 로직 포함
- 데이터가 없는 티커는 `no_data_tickers.csv`로 별도 저장

### 3. 주요 지수 데이터 수집

다음과 같은 주요 미국 시장 지수 데이터를 수집할 수 있습니다.

- NASDAQ Composite
- NASDAQ-100
- S&P 500
- Dow Jones 30
- Russell 2000
- NYSE Composite
- Wilshire 5000
- VIX

### 4. 섹터 ETF 데이터 수집

섹터별 ETF 데이터를 수집해 시장 흐름을 비교할 수 있도록 구성했습니다.

예시 수집 대상:

- XLC
- XLY
- XLP
- XLE
- XLF
- XLV
- XLI
- XLK
- XLB
- XLRE
- XLU

### 5. HTML 차트 뷰어

- Chart.js / Lightweight Charts 기반 CSV 시각화
- 저장된 지수 데이터를 캔들 차트로 확인
- 실시간 스트리밍이 아니라, 수집된 CSV 데이터를 기준으로 확인하는 구조

---

## 설치 및 설정

### 1. 의존성 설치

```bash
pip install -r requirements.txt
```

### 2. 주요 패키지

| Package | Purpose |
|---|---|
| `pandas` | CSV 데이터 처리 |
| `requests` | NASDAQ 티커 목록 수집 |
| `yfinance` | Yahoo Finance 기반 가격 데이터 수집 |

---

## 사용법

### 1. NASDAQ 티커 목록 수집

```bash
python nasdaq_ticker_fetch.py
```

실행 결과:

- NASDAQ 상장 티커 목록 수집
- `nasdaq_tickers.csv` 파일 생성
- 수집 당시 기준 약 3,900개 이상의 티커 저장

### 2. 개별 종목 가격 데이터 수집

```bash
python nasdaq_stock_crawler.py
```

실행 시 다음 정보를 입력합니다.

| 입력 항목 | 예시 |
|---|---|
| 데이터 조회 기간 | `max`, `1y`, `6mo`, `30d` |
| 저장할 봉 단위 | `1m`, `2m`, `5m`, `15m`, `1h`, `1d` |

저장 결과:

```text
nasdaq_stock_data/{TICKER}_{INTERVAL}.csv
```

예시:

```text
nasdaq_stock_data/AAPL_1d.csv
nasdaq_stock_data/MSFT_1d.csv
```

### 3. 주요 지수 데이터 수집

```bash
python nasdaq_index_crawler.py
```

실행 시 다음 정보를 입력합니다.

| 입력 항목 | 예시 |
|---|---|
| 조회 기간 | `1y`, `5y`, `10y`, `max` |
| 봉 단위 | `1d`, `1wk`, `1mo` |

저장 결과:

```text
nasdaq_index_data/{INDEX_NAME}_{INTERVAL}.csv
```

예시:

```text
nasdaq_index_data/NASDAQ_Composite_1d.csv
nasdaq_index_data/SP500_1d.csv
```

### 4. 섹터 ETF 데이터 수집

```bash
python nasdaq_etf_crawler.py
```

저장 결과:

```text
nasdaq_etf_data/{ETF}_{INTERVAL}.csv
```

---

## 데이터 저장 형식

### 개별 종목 데이터

저장 위치:

```text
nasdaq_stock_data/
```

파일명 형식:

```text
{TICKER}_{INTERVAL}.csv
```

예시:

```text
AAPL_1d.csv
MSFT_1d.csv
NVDA_1d.csv
```

| Column | Description | Type |
|---|---|---|
| Date | 날짜 및 시간 | datetime |
| Open | 시가 | float |
| High | 고가 | float |
| Low | 저가 | float |
| Close | 종가 | float |
| Volume | 거래량 | int |
| Dividends | 배당금 | float |
| Stock Splits | 주식 분할 | float |

예시:

```csv
Date,Open,High,Low,Close,Volume,Dividends,Stock Splits
1980-12-12 00:00:00-05:00,0.128348,0.128348,0.128348,0.128348,117258400,0.0,0.0
1980-12-15 00:00:00-05:00,0.122767,0.122767,0.122767,0.122767,43971200,0.0,0.0
```

### 주요 지수 데이터

저장 위치:

```text
nasdaq_index_data/
```

파일명 형식:

```text
{INDEX_NAME}_{INTERVAL}.csv
```

예시:

```text
NASDAQ_Composite_1d.csv
SP500_1d.csv
Dow_Jones_1d.csv
```

| Column | Description | Type |
|---|---|---|
| Date | 날짜 | date |
| Open | 시가 | float |
| High | 고가 | float |
| Low | 저가 | float |
| Close | 종가 | float |
| Volume | 거래량 | int |

예시:

```csv
Date,Open,High,Low,Close,Volume
1971-02-05,100.0,100.0,100.0,100.0,0
1971-02-08,100.83999633789062,100.83999633789062,100.83999633789062,100.83999633789062,0
```

---

## 크롤링 요령

### 1. 권장 수집 순서

```text
티커 목록 수집
↓
주요 지수 데이터 수집
↓
섹터 ETF 데이터 수집
↓
개별 종목 데이터 수집
```

명령어 기준:

```bash
python nasdaq_ticker_fetch.py
python nasdaq_index_crawler.py
python nasdaq_etf_crawler.py
python nasdaq_stock_crawler.py
```

### 2. 시간 단위별 권장 설정

| 목적 | 권장 기간 | 권장 봉 단위 |
|---|---|---|
| 장기 분석 / 백테스팅 | `max`, `10y`, `5y` | `1d`, `1wk`, `1mo` |
| 중기 분석 | `1y`, `2y` | `1h`, `1d` |
| 단기 분석 | `1mo`, `3mo`, `6mo` | `1m`, `5m`, `15m` |

### 3. 오류 처리

- 네트워크 오류 발생 시 재시도
- 데이터가 없는 티커는 `no_data_tickers.csv`로 별도 저장
- 요청 사이에 지연 시간을 두어 API 제한 가능성 완화
- 일부 티커는 yfinance 응답 형식이나 상장 상태에 따라 데이터가 없을 수 있음

### 4. 저장 공간 관리

대량 수집 시 저장 공간을 고려해야 합니다.

| Data Type | Expected Size |
|---|---|
| 일봉 데이터 | 약 3,900개 파일 기준 수백 MB 수준 |
| 시간봉 데이터 | 기간에 따라 GB 단위 가능 |
| 분봉 데이터 | 수집 기간이 길면 용량이 크게 증가 가능 |

분봉 데이터는 필요한 기간만 제한해서 수집하는 것을 권장합니다.

---

## 파일 구조

```text
Nasdaq_crawler
├── nasdaq_ticker_fetch.py       # NASDAQ 티커 목록 수집
├── nasdaq_stock_crawler.py      # 개별 종목 가격 데이터 수집
├── nasdaq_index_crawler.py      # 주요 지수 데이터 수집
├── nasdaq_etf_crawler.py        # 섹터 ETF 데이터 수집
├── requirements.txt             # Python 의존성
├── nasdaq_tickers.csv           # 수집된 티커 목록
├── no_data_tickers.csv          # 데이터 없는 티커 목록
├── nasdaq_stock_data/           # 개별 종목 데이터 저장 폴더
│   ├── AAPL_1d.csv
│   ├── MSFT_1d.csv
│   └── ...
├── nasdaq_index_data/           # 주요 지수 데이터 저장 폴더
│   ├── NASDAQ_Composite_1d.csv
│   ├── SP500_1d.csv
│   └── ...
├── nasdaq_etf_data/             # 섹터 ETF 데이터 저장 폴더
│   ├── XLF_1d.csv
│   ├── XLK_1d.csv
│   └── ...
├── viewer3.html                 # 차트 뷰어
├── TradingView.html             # Lightweight Charts 기반 차트 뷰어
└── Chart_js.html                # Chart.js 기반 차트 뷰어
```

---

## 데이터 활용 예시

### 1. Python에서 데이터 로드

```python
import pandas as pd

df = pd.read_csv("nasdaq_stock_data/AAPL_1d.csv")
df["Date"] = pd.to_datetime(df["Date"])
df.set_index("Date", inplace=True)

index_df = pd.read_csv("nasdaq_index_data/NASDAQ_Composite_1d.csv")
index_df["Date"] = pd.to_datetime(index_df["Date"])
index_df.set_index("Date", inplace=True)
```

### 2. 이동평균 계산

```python
df["MA20"] = df["Close"].rolling(window=20).mean()
df["MA50"] = df["Close"].rolling(window=50).mean()
```

### 3. RSI 계산

```python
def calculate_rsi(prices, period=14):
    delta = prices.diff()

    gain = delta.where(delta > 0, 0).rolling(window=period).mean()
    loss = (-delta.where(delta < 0, 0)).rolling(window=period).mean()

    rs = gain / loss
    return 100 - (100 / (1 + rs))

df["RSI"] = calculate_rsi(df["Close"])
```

### 4. 간단한 이동평균 크로스오버 예시

```python
def backtest_ma_crossover(df):
    df = df.copy()

    df["MA20"] = df["Close"].rolling(window=20).mean()
    df["MA50"] = df["Close"].rolling(window=50).mean()

    df["Signal"] = 0
    df.loc[df["MA20"] > df["MA50"], "Signal"] = 1
    df.loc[df["MA20"] < df["MA50"], "Signal"] = -1

    df["Returns"] = df["Close"].pct_change()
    df["Strategy_Returns"] = df["Signal"].shift(1) * df["Returns"]

    return df["Strategy_Returns"].cumsum()
```

### 5. 데이터 시각화

```python
import matplotlib.pyplot as plt

plt.figure(figsize=(12, 6))
plt.plot(df.index, df["Close"], label="Close Price")
plt.plot(df.index, df["MA20"], label="20-day MA")
plt.plot(df.index, df["MA50"], label="50-day MA")
plt.title("AAPL Stock Price with Moving Averages")
plt.legend()
plt.show()
```

---

## 주의사항

1. Yahoo Finance 기반 데이터는 참고용으로 사용해야 합니다.
2. 투자 판단에 사용할 경우 공식 데이터와 교차 확인이 필요합니다.
3. 일부 티커는 상장폐지, 거래정지, 데이터 누락 등으로 수집되지 않을 수 있습니다.
4. 대량 수집 시 요청 제한, 네트워크 오류, 저장 공간 부족이 발생할 수 있습니다.
5. 분봉 데이터는 기간 제한이 있을 수 있으므로 필요한 범위만 수집하는 것이 좋습니다.

---

## 현재 상태

현재는 추가 개발이나 실시간 운용 중인 프로젝트가 아니라,  
미국 주식 시장 데이터 수집과 시각화 구조를 실험한 뒤 보관 중인 도구입니다.

NASDAQ 티커, 개별 종목, 주요 지수, 섹터 ETF 데이터를 수집하고 차트로 확인할 수 있는 기반을 만들었지만,  
현재는 다른 분석 도구와 우선순위 차이로 인해 완성 후 사용 보류 상태로 유지하고 있습니다.

---

## 업데이트

데이터를 최신 상태로 유지하려면 필요한 시점에 크롤링 스크립트를 다시 실행합니다.

권장 갱신 방식:

| Data | Update Timing |
|---|---|
| 일봉 데이터 | 장 마감 후 필요 시 재수집 |
| 시간봉 데이터 | 분석 목적에 따라 필요 시 재수집 |
| 분봉 데이터 | 필요한 기간만 제한적으로 재수집 |

---

## 문의

프로젝트 관련 문의사항이나 개선 제안은 GitHub Issue를 통해 남길 수 있습니다.
