# Volume Anomaly Detector — Institutional Order Flow

A machine learning system that detects unusual volume activity in 
BTC/USD 5M data — identifying potential institutional order flow 
using CVD analysis and statistical anomaly detection.

Exported as a TradingView Pine Script v6 indicator with 
multi-timeframe support.

## The Problem

Retail traders struggle to identify when institutional players 
enter the market. A breakout without institutional backing often 
fails — one with it tends to follow through.

The question: **can we detect institutional activity automatically 
using volume and CVD analysis?**

## The Solution

A two-layer anomaly detection system:

```
Layer 1 — Statistical (Rolling Z-score)
  Volume z-score > 3.0 standard deviations locally
  Candle size z-score > 2.0 standard deviations locally
  Volume ratio > 2.0x local average

Layer 2 — Cluster Filter
  Maximum 2 anomalies per 10-bar window
  Isolates genuine events from sustained high-volume periods
```

## Results

| Period          | Price Change | Anomalies | Rate  |
|-----------------|-------------|-----------|-------|
| October 2023    | +27.2%      | 78        | 0.90% |
| March 2024 ATH  | +14.0%      | 11        | 0.13% |
| August 2024     | -8.8%       | 11        | 0.13% |
| Full dataset    | —           | 1,184     | 0.34% |

Key finding: October 2023 showed 3x more anomalies than average — 
consistent with the explosive institutional accumulation that drove 
BTC from $25K to $35K ahead of the 2024 halving.

## TradingView Indicator

Features:
- Bullish 🐋 triangle below bar — buying institutional activity
- Bearish 🐋 triangle above bar — selling institutional activity
- Yellow background highlight on anomaly bars
- **Multi-timeframe support** — plot 5M signals on 1M chart
- 3 alert conditions — bullish, bearish, any anomaly

## Tech Stack

- **Detection**: Isolation Forest + Rolling Z-score
- **Data**: MT5 BTC/USD 5M — 346,078 bars (2023-2026)
- **Export**: Pine Script v6 — TradingView
- **Language**: Python 3.10

## Project Structure

```
volume-anomaly-detector/
├── notebooks/
│   ├── 01_feature_engineering.ipynb  # CVD and volume features
│   └── 02_anomaly_detection.ipynb    # Model training and validation
├── models/
│   ├── anomaly_model.pkl             # Trained Isolation Forest
│   ├── anomaly_scaler.pkl            # StandardScaler
│   └── anomaly_params.pkl            # Final detection parameters
├── indicator.pine                    # TradingView Pine Script v6
└── requirements.txt
```

## Key Features

```
vol_delta        directional volume per candle
CVD_50/100       rolling cumulative volume delta (4H/8H)
vol_ratio        current volume vs 20-bar moving average
vol_zscore       standard deviations from local mean volume
candle_size      candle body size as % of price
divergence       CVD vs price direction mismatch
```

## Key Technical Decisions

**Why Rolling Z-score over pure Isolation Forest?**
Isolation Forest trained on the full dataset flagged 4% of bars 
in October 2023 — a genuinely extreme month that distorted the 
global "normal". Rolling z-score adapts to local market conditions, 
making anomaly definition relative to recent behavior.

**Why cluster filter?**
Sustained high-volume periods (trending markets) should not be 
flagged continuously. The filter isolates genuinely isolated events 
from structural market changes.

**Why Pine Script export?**
The ultimate goal is actionable signals for traders. TradingView 
is the industry standard — exporting to Pine Script makes the 
detector immediately usable in live trading with alerts.
