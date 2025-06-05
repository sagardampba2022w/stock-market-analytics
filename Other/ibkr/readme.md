# IBKR Python API Setup & Quick Start (with ib_insync)

## 1. Open and Fund Your IBKR Account
- Register at [Interactive Brokers](https://www.interactivebrokers.com/)
- Fund your account (optional for paper trading)

## 2. Download and Install Trader Workstation (TWS)
- Download [IBKR TWS here](https://www.interactivebrokers.com/en/index.php?f=16040)
- Install and log in (choose **Paper Trading** mode for safety while testing)

## 3. Configure TWS for API Access
- In TWS, go to:  
  **File → Global Configuration → API → Settings**
  - Check: **Enable ActiveX and Socket Clients**
  - (Optional) Check: **Read-Only API** (safer for testing)
  - Note your **API port** (`7497` = paper, `7496` = live, unless changed)
  - Ensure: **Allow connections from localhost only** is ON

## 4. Install Python and Required Packages
- Install [Python 3.x](https://www.python.org/)
- In your terminal or command prompt, install these libraries:
  ```bash
  pip install ib_insync nest_asyncio pandas


## 5. Start TWS and Log In
- Open TWS and log in (Paper Trading recommended for testing)
- Ensure your API settings (see step 3) are still correct

## 6. Test Your Python Connection

```python
import nest_asyncio
nest_asyncio.apply()
from ib_insync import *

ib = IB()
ib.connect('127.0.0.1', 7497, clientId=1)  # Use 7496 for live

print("Connection successful:", ib.isConnected())
ib.disconnect()
```

## 7. Example: Fetch Historical Data (AAPL, 5-minute bars, 1 day)

```python
import nest_asyncio
nest_asyncio.apply()
from ib_insync import *

ib = IB()
ib.connect('127.0.0.1', 7497, clientId=1)

contract = Stock('AAPL', 'SMART', 'USD')
bars = ib.reqHistoricalData(
    contract,
    endDateTime='',
    durationStr='1 D',
    barSizeSetting='5 mins',
    whatToShow='TRADES',
    useRTH=True,
    formatDate=1
)

import pandas as pd
df = util.df(bars)
print(df.head())

ib.disconnect()
```


## 8.Example: Get Portfolio & Account Summary

```python
import nest_asyncio
nest_asyncio.apply()
from ib_insync import *

ib = IB()
ib.connect('127.0.0.1', 7497, clientId=2)

# Portfolio positions
portfolio = ib.portfolio()
print("Portfolio:")
for pos in portfolio:
    print(pos.contract.symbol, pos.position, pos.marketValue)

# Account summary
summary = ib.accountSummary()
for item in summary:
    print(item.tag, item.value, item.currency)

ib.disconnect()
```


## 9. Troubleshooting
"client id already in use": Use a different clientId or restart TWS.

"Requested market data requires subscription": You may need to purchase live data for that instrument.

If using Jupyter Notebook: always run import nest_asyncio; nest_asyncio.apply() before connecting.
