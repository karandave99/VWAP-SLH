# Python Learning Path - From Zero to Algo Trader

## For Complete Beginners - Step by Step

---

## 📚 Prerequisites

**Before starting:**
- ✅ You've validated your strategy in Pine Script (Phase 1 complete)
- ✅ You know what signals you're looking for
- ✅ You have 1-2 hours per day to learn
- ✅ You're ready to make mistakes and learn from them

**What you DON'T need:**
- ❌ Any coding experience
- ❌ Math degree
- ❌ Expensive courses
- ❌ Special computer (any laptop works)

---

## 🎯 Week 1: Python Fundamentals

### Day 1: Setup & Your First Program (1 hour)

**Install:**
1. Python 3.11+ from python.org
2. VSCode (free editor)
3. Python extension for VSCode

**Your First Code:**
```python
# This is hello_world.py
print("Hello, Trading World!")

price = 50000  # Bitcoin price
print(f"BTC price is ${price}")
```

**Run it:**
```bash
python hello_world.py
```

**What you'll learn:**
- Variables (storing data)
- Print statements (showing output)
- Comments (notes in your code)

---

### Day 2: Numbers & Math (1 hour)

**Learn:**
```python
# Basic math for trading
entry_price = 50000
exit_price = 51000
profit = exit_price - entry_price
profit_percent = (profit / entry_price) * 100

print(f"Profit: ${profit} ({profit_percent}%)")

# Lists - storing multiple prices
prices = [50000, 50500, 49800, 51000, 50200]
print(f"First price: {prices[0]}")
print(f"Last price: {prices[-1]}")
print(f"Number of prices: {len(prices)}")
```

**Exercise:**
Calculate profit/loss from your last 5 trades

---

### Day 3: Loops & Conditions (1 hour)

**Learn:**
```python
# Loop through prices
prices = [50000, 50500, 49800, 51000, 50200]

for price in prices:
    print(f"Current price: ${price}")

# Conditions for trading signals
vwap = 50000
current_price = 51000

if current_price > vwap:
    print("Price above VWAP - Bullish")
elif current_price < vwap:
    print("Price below VWAP - Bearish")
else:
    print("Price at VWAP - Neutral")
```

**Exercise:**
Loop through prices and count how many are above VWAP

---

### Day 4: Functions (1 hour)

**Learn:**
```python
# Create reusable functions
def calculate_vwap(prices, volumes):
    total_value = sum(p * v for p, v in zip(prices, volumes))
    total_volume = sum(volumes)
    return total_value / total_volume

# Use your function
prices = [100, 101, 99, 102]
volumes = [1000, 1500, 800, 1200]

vwap = calculate_vwap(prices, volumes)
print(f"VWAP: {vwap:.2f}")
```

**Exercise:**
Create function to calculate profit percentage

---

### Day 5: Dictionaries & Data Structures (1 hour)

**Learn:**
```python
# Store trade data
trade = {
    "symbol": "BTC/USD",
    "entry": 50000,
    "exit": 51000,
    "size": 0.1,
    "profit": 100
}

print(f"Trade: {trade['symbol']}")
print(f"Profit: ${trade['profit']}")

# List of trades
trades = [
    {"symbol": "BTC", "profit": 100},
    {"symbol": "ETH", "profit": -50},
    {"symbol": "BTC", "profit": 200}
]

total_profit = sum(t["profit"] for t in trades)
print(f"Total profit: ${total_profit}")
```

**Exercise:**
Store your Pine Script test results as dictionaries

---

### Day 6-7: Mini Project - Trade Analyzer (2 hours)

**Build:**
```python
# trade_analyzer.py
def analyze_trades(trades):
    """Analyze a list of trades"""
    total_profit = sum(t["profit"] for t in trades)
    wins = [t for t in trades if t["profit"] > 0]
    losses = [t for t in trades if t["profit"] < 0]

    win_rate = len(wins) / len(trades) * 100
    avg_win = sum(t["profit"] for t in wins) / len(wins) if wins else 0
    avg_loss = sum(t["profit"] for t in losses) / len(losses) if losses else 0

    return {
        "total_profit": total_profit,
        "win_rate": win_rate,
        "avg_win": avg_win,
        "avg_loss": avg_loss
    }

# Your trades from Pine Script testing
my_trades = [
    {"date": "2025-01-01", "profit": 150},
    {"date": "2025-01-02", "profit": -50},
    {"date": "2025-01-03", "profit": 200},
    # ... add more
]

results = analyze_trades(my_trades)
print(f"Win Rate: {results['win_rate']:.1f}%")
print(f"Total Profit: ${results['total_profit']}")
```

**Goal:** Analyze your Pine Script test results with Python

---

## 🐼 Week 2: Pandas & Data Manipulation

### Day 1: Install & Load Data (1 hour)

**Install:**
```bash
pip install pandas yfinance matplotlib
```

**Your First DataFrame:**
```python
import pandas as pd
import yfinance as yf

# Download Bitcoin data
btc = yf.download("BTC-USD", start="2025-01-01", end="2025-01-31")
print(btc.head())  # First 5 rows
print(btc.tail())  # Last 5 rows

# Access columns
print(btc["Close"])  # Closing prices
print(btc["Volume"])  # Volume
```

---

### Day 2: Calculate VWAP (1.5 hours)

**Learn:**
```python
import pandas as pd
import yfinance as yf

# Download data
df = yf.download("BTC-USD", start="2025-01-01", interval="15m")

# Calculate VWAP
df["Typical_Price"] = (df["High"] + df["Low"] + df["Close"]) / 3
df["TP_Volume"] = df["Typical_Price"] * df["Volume"]
df["Cumulative_TP_Vol"] = df["TP_Volume"].cumsum()
df["Cumulative_Volume"] = df["Volume"].cumsum()
df["VWAP"] = df["Cumulative_TP_Vol"] / df["Cumulative_Volume"]

print(df[["Close", "VWAP"]].tail())
```

**Exercise:**
Calculate VWAP for different timeframes

---

### Day 3: Detect Crosses (1.5 hours)

**Learn:**
```python
# Detect VWAP crosses
df["Above_VWAP"] = df["Close"] > df["VWAP"]
df["Cross_Up"] = (df["Above_VWAP"]) & (~df["Above_VWAP"].shift(1))
df["Cross_Down"] = (~df["Above_VWAP"]) & (df["Above_VWAP"].shift(1))

# Find all crosses
crosses_up = df[df["Cross_Up"]]
crosses_down = df[df["Cross_Down"]]

print(f"Found {len(crosses_up)} crosses up")
print(f"Found {len(crosses_down)} crosses down")
```

---

### Day 4: Pivot Detection (2 hours)

**Learn:**
```python
def find_pivots(df, period=5):
    """Find pivot highs and lows"""
    pivots = []

    for i in range(period, len(df) - period):
        # Check if current high is highest in window
        is_pivot_high = all(df["High"].iloc[i] > df["High"].iloc[i-period:i]) and \
                        all(df["High"].iloc[i] > df["High"].iloc[i+1:i+period+1])

        # Check if current low is lowest in window
        is_pivot_low = all(df["Low"].iloc[i] < df["Low"].iloc[i-period:i]) and \
                       all(df["Low"].iloc[i] < df["Low"].iloc[i+1:i+period+1])

        if is_pivot_high:
            pivots.append({"index": i, "type": "high", "price": df["High"].iloc[i]})
        elif is_pivot_low:
            pivots.append({"index": i, "type": "low", "price": df["Low"].iloc[i]})

    return pd.DataFrame(pivots)

pivots = find_pivots(df, period=8)
print(pivots.head())
```

---

### Day 5-7: Build ZigZag Class (3 hours)

**Learn:**
```python
class ZigZag:
    def __init__(self, period=5):
        self.period = period
        self.pivots = []
        self.direction = None

    def update(self, df):
        """Find all pivots in dataframe"""
        # Implementation here
        pass

    def get_current_pivot(self):
        """Get most recent pivot"""
        if self.pivots:
            return self.pivots[-1]
        return None

    def get_pattern(self):
        """Detect HH/LL/LH/HL"""
        if len(self.pivots) < 3:
            return None

        # Logic to determine pattern
        pass

# Usage
zz = ZigZag(period=8)
zz.update(df)
print(f"Current pivot: {zz.get_current_pivot()}")
```

**Goal:** Recreate your ZigZagLib.pine in Python

---

## 📈 Week 3: Backtesting Framework

### Day 1-2: Install Vectorbt (2 hours)

**Install:**
```bash
pip install vectorbt
```

**Simple Backtest:**
```python
import vectorbt as vbt
import yfinance as yf

# Download data
df = yf.download("BTC-USD", start="2024-01-01", end="2025-01-01")

# Simple strategy: Buy when above VWAP
df["VWAP"] = vbt.talib("VWAP").run(df["High"], df["Low"], df["Close"], df["Volume"]).vwap
entries = df["Close"] > df["VWAP"]
exits = df["Close"] < df["VWAP"]

# Run backtest
portfolio = vbt.Portfolio.from_signals(df["Close"], entries, exits)

# Results
print(portfolio.stats())
print(f"Total Return: {portfolio.total_return():.2%}")
print(f"Sharpe Ratio: {portfolio.sharpe_ratio():.2f}")
```

---

### Day 3-5: Build VWAP Liquidity Strategy (4 hours)

**Your Strategy:**
```python
class VWAPLiquidityStrategy:
    def __init__(self, htf_period=8, ltf_period=2):
        self.htf_zz = ZigZag(htf_period)
        self.ltf_zz = ZigZag(ltf_period)
        self.liquidity_zones = []

    def run(self, df):
        """Run strategy on dataframe"""
        # 1. Calculate VWAP
        # 2. Find HTF/LTF pivots
        # 3. Detect VWAP crosses
        # 4. Create liquidity zones
        # 5. Generate signals
        pass

    def backtest(self, df):
        """Backtest strategy"""
        signals = self.run(df)
        portfolio = vbt.Portfolio.from_signals(
            df["Close"],
            signals["entry"],
            signals["exit"]
        )
        return portfolio

# Usage
strategy = VWAPLiquidityStrategy(htf_period=8, ltf_period=2)
results = strategy.backtest(df)
print(results.stats())
```

---

### Day 6-7: Optimize & Analyze (3 hours)

**Optimization:**
```python
# Test different parameters
results = []

for htf in [5, 8, 10, 13]:
    for ltf in [1, 2, 3]:
        strategy = VWAPLiquidityStrategy(htf, ltf)
        portfolio = strategy.backtest(df)

        results.append({
            "htf": htf,
            "ltf": ltf,
            "return": portfolio.total_return(),
            "sharpe": portfolio.sharpe_ratio(),
            "win_rate": portfolio.trades.win_rate()
        })

# Find best parameters
best = max(results, key=lambda x: x["sharpe"])
print(f"Best: HTF={best['htf']}, LTF={best['ltf']}")
```

---

## 📊 Week 4: Visualization & Charting

### Day 1-3: Plotly Charts (3 hours)

**Learn:**
```python
import plotly.graph_objects as go

# Create candlestick chart
fig = go.Figure(data=[go.Candlestick(
    x=df.index,
    open=df["Open"],
    high=df["High"],
    low=df["Low"],
    close=df["Close"]
)])

# Add VWAP
fig.add_trace(go.Scatter(
    x=df.index,
    y=df["VWAP"],
    name="VWAP",
    line=dict(color="blue", width=2)
))

# Add liquidity zones (lines)
# Add entry/exit markers

fig.show()
```

---

### Day 4-7: Interactive Dashboard (4 hours)

**Build with Streamlit:**
```python
# dashboard.py
import streamlit as st
import yfinance as yf
import plotly.graph_objects as go

st.title("VWAP Liquidity Strategy")

# Sidebar inputs
symbol = st.sidebar.text_input("Symbol", "BTC-USD")
htf_period = st.sidebar.slider("HTF Period", 5, 20, 8)
ltf_period = st.sidebar.slider("LTF Period", 1, 5, 2)

# Download data
df = yf.download(symbol, period="1mo", interval="15m")

# Run strategy
strategy = VWAPLiquidityStrategy(htf_period, ltf_period)
results = strategy.backtest(df)

# Display metrics
col1, col2, col3 = st.columns(3)
col1.metric("Total Return", f"{results.total_return():.2%}")
col2.metric("Win Rate", f"{results.trades.win_rate():.1%}")
col3.metric("Sharpe Ratio", f"{results.sharpe_ratio():.2f}")

# Display chart
fig = create_strategy_chart(df, strategy)
st.plotly_chart(fig)
```

Run with: `streamlit run dashboard.py`

---

## 🤖 Week 5-6: Live Trading Setup

### Week 5: Paper Trading

**Setup Alpaca (Free Paper Trading):**
```python
import alpaca_trade_api as tradeapi

# Get free API keys from alpaca.markets
api = tradeapi.REST(
    key_id="YOUR_KEY",
    secret_key="YOUR_SECRET",
    base_url="https://paper-api.alpaca.markets"  # Paper trading
)

# Get account info
account = api.get_account()
print(f"Buying Power: ${account.buying_power}")

# Place order
api.submit_order(
    symbol="BTCUSD",
    qty=0.01,
    side="buy",
    type="market",
    time_in_force="gtc"
)
```

---

### Week 6: Trading Bot

**Build Live Bot:**
```python
import time
from datetime import datetime

class TradingBot:
    def __init__(self, strategy, api):
        self.strategy = strategy
        self.api = api
        self.is_running = False

    def run(self):
        """Main trading loop"""
        self.is_running = True

        while self.is_running:
            try:
                # 1. Get latest data
                df = self.get_latest_data()

                # 2. Run strategy
                signal = self.strategy.get_signal(df)

                # 3. Execute trade if signal
                if signal == "BUY":
                    self.buy()
                elif signal == "SELL":
                    self.sell()

                # 4. Wait before next check
                time.sleep(60)  # Check every minute

            except Exception as e:
                print(f"Error: {e}")
                time.sleep(60)

    def buy(self):
        """Execute buy order"""
        pass

    def sell(self):
        """Execute sell order"""
        pass

# Usage
bot = TradingBot(strategy, api)
bot.run()  # Run in paper mode for 2 weeks minimum!
```

---

## 📋 Week-by-Week Checklist

### Week 1: Python Basics ✅
- [ ] Setup Python & VSCode
- [ ] Learn variables, loops, conditions, functions
- [ ] Build trade analyzer script
- [ ] Complete all daily exercises

### Week 2: Pandas & Trading Math ✅
- [ ] Install pandas, yfinance
- [ ] Download & explore price data
- [ ] Calculate VWAP in Python
- [ ] Detect crosses and pivots
- [ ] Build ZigZag class

### Week 3: Backtesting ✅
- [ ] Install vectorbt
- [ ] Run simple VWAP backtest
- [ ] Build full VWAP Liquidity strategy class
- [ ] Optimize parameters
- [ ] Compare results to Pine Script

### Week 4: Visualization ✅
- [ ] Create candlestick charts with Plotly
- [ ] Add strategy signals to charts
- [ ] Build Streamlit dashboard
- [ ] Interactive parameter testing

### Week 5: Paper Trading ✅
- [ ] Sign up for Alpaca paper account
- [ ] Connect API and test
- [ ] Place manual test orders
- [ ] Monitor paper account

### Week 6: Live Bot ✅
- [ ] Build trading bot script
- [ ] Add risk management (stop loss, position sizing)
- [ ] Add logging and notifications
- [ ] Run in paper mode for 2+ weeks
- [ ] Track performance vs backtest

---

## 📚 Resources for Learning

### Free Python Courses:
1. **Codecademy** - Learn Python 3 (free tier)
2. **Python.org Tutorial** - Official docs
3. **Real Python** - Practical tutorials

### Trading-Specific:
1. **Quantopian Lectures** (archived) - Free algo trading course
2. **QuantConnect University** - Free lessons
3. **Backtrader Docs** - Backtesting framework

### Communities:
1. **r/algotrading** - Reddit community
2. **QuantConnect Forum** - Q&A
3. **Python Discord** - General Python help

---

## 💰 Costs

**Total cost: $0-50/month**

- Python: FREE
- VSCode: FREE
- Libraries (pandas, vectorbt, etc.): FREE
- Alpaca Paper Trading: FREE
- Data (yfinance): FREE
- TradingView (optional): $15-60/month

---

## ⚠️ Important Warnings

1. **Paper trade for AT LEAST 2 weeks** before real money
2. **Start with tiny position sizes** (0.1% of capital)
3. **Never risk more than 1-2% per trade**
4. **Backtest results ≠ future results**
5. **Have stop losses ALWAYS**
6. **Don't trade if you can't afford to lose it**

---

## 🎯 Your Success Criteria

**After 6 weeks, you should have:**
- ✅ Python skills to build trading strategies
- ✅ VWAP Liquidity strategy fully coded
- ✅ Backtested with historical data
- ✅ Interactive dashboard to monitor
- ✅ Paper trading results (2+ weeks)
- ✅ Confidence to continue learning

---

## What's Next After Week 6?

1. **Run paper trading for 1-3 months** minimum
2. **Add more strategies** (order blocks, FVG, etc.)
3. **Learn machine learning** for prediction
4. **Build portfolio of strategies**
5. **Consider real money** (if paper trading profitable for 3+ months)

---

**Don't start this until Phase 1 (Pine Script) is complete!**

**Bookmark this file and come back when ready.**
