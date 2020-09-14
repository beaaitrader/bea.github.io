---
layout: default
title: Data sources
description: Different data sources for Algo trader
---

```python

import pandas as pd
import pandas_datareader.data as pdr
from datetime import datetime
import matplotlib.pyplot as plt
plt.style.use('seaborn')
import json
import requests

```

### FRED (Federal Reserve Economic Data): Economic and market data
* The most comprehensive  repository of US economic time series data. It has more than half a million economic time series from 87 sources.
It covers banking, business/fiscal, consumer price indexes, employment and population, exchange rates, gross domestic product, interest rates, monetary aggregates, producer price 
indexes, reserves and monetary base, U.S. trade and international transactions, and U.S. financial data. 
The time series are complied by Federal Reserve and many are collected from government agencies such as the U.S. Census and the Bureau of Labor Statistics

```python

start = datetime(2019, 1, 1)
end = datetime(2020, 1, 1)

inflation = pdr.DataReader('T5YIE', 'fred', start, end)
inflation.plot(figsize=(20, 5), title='5 years forward inflation expectation rate'), plt.show();

```

### Alpha Vantage: Equities, currencies, cryptocurrentcies, sectors, indicators
* Repository of ree APIs for realtime (upto 1 minute) and historical data (upto 20 years). 
Suggested usage is 5 API requests per minute and 500 requests per day with your own API key.
APIs are grouped into four categories: 
   * Equity
   * Currencies ( including cryptocurrencies)
   * Sectors
   * Technical indicators
Run buy a tight-knit community of researchers, engineers, and business professionals.
Website supports `VSV` and `JSON` formats.

* You can find the API documentation here: https://www.alphavantage.co/documentation

```python
response = requests.get('https://www.alphavantage.co/query?function=FX_DAILY&from_symbol=EUR&to_symbol=USD&apikey=demo')
alphadict = json.load(response.text)
eur = pd.DataFrame(alphadict['Time Series FX (Daily)']).T
eur.index = pd.to_datetime(eur.index)
eur = eur.sort_index(ascending = True)
eur.columns = ['open', 'high', 'low', 'close']
eur = eur.astype(float)
eur.head()

```

### Yahoo Finance: free on stop shop for various financial securities
* Unstable endpoints and data header needs a bug fix
* This is the grand daddy of financial information. 
It has a vast repository of historical data that cover most traded securities. 
There is a pandas datareader that requires a bug fix which is provided below. However, the API is not reliable and will not return data sometimes.
Fix of yahoo data: [https://github.com/ranaroussi/yfinance](https://github.com/ranaroussi/yfinance)
```python
import yfinance as yf
yf.pdr_override()
stock = pdr.get_data_yahoo("AMZN", start, end)
pprint(stock.head(10))
```

### Quandl: One stop shop for all kinds of data
* Free and premium APIs
* A one stop shop for economic, financial and sentiment data some of it s offered for free and most others for a fee.
Quandl sources data from over half a million publishers worldwide. It was acquired by NASDAQ in 2018.
It sources freely available public sources like FRED and private source of alternative data. 
Many freely available data, such as historical equity data, are offered for a fee.
Anonymous users have a limit of 20 calls per 10 minutes and 50 calls per day. 
Authenticated users have a limit of 300 calls per 10 seconds, 2000 calls per 10 minutes and a limit of 50,000 calls per day.
Authenticated users of free data feeds have a concurrency limit of one; that is, they can make one call at a time and have an additional call in the queue.
See API documentation there: [https://docs.quandl.com/ ](https://docs.quandl.com/)

`pip3.8 install quandl`

```python
import quandl
investor_sentiment = quandl.get('AAII/AAII_SENTIMENT', start_date=start, end_date=end)
investor_sentiment['Bull-Bear Spread'].plot(figsize=(20, 5), title='American Association of Individual Investor bull-bear spread sentiment')
plt.show()
pprint(investor_sentiment.head())
```
### IEX Cloud: Equity, ETF, market and fundamental data

* The investors Exchange (IEX) was founded by brad Katsuyama, hero of the book `Flash Boys` by Michael Lewis
IEX recently launched IEX cloud, a new platform provides market and fundamental data for free and for a fee.
The default data format is JSON. See code snippets below.

```python
response = requests.get('https://sandbox.iexapis.com/stable/aapl/financials?token=Tpk_53e30ef0593440d5855c259602cad185')
jdictonary = json.loads(response.text)
financials = pd.DataFrame(jdictonary['financials'])
financials
```

### EDGAR (Electronic Data Gathering, Analysis, and Retrieval system): Company fundamental data
* Comprehensive repository about companies, both domestic and foreign, who are required by law to file several forms like 10k and 10Q.
Third-party filings with respect to these companies, such as tender offers and Schedule 12D filings, are also filed via EDGRA.
Not all SEC filings by public companies are available on EDGRA. Companies were phrased to EDGAR filing over a three-year period, ending 6 May 1996.
Information is copious and needs to be parsed.
For more information go here: [https://www.sec.gov/edgar/searchedgar/accessing-edgar-data.htm](https://www.sec.gov/edgar/searchedgar/accessing-edgar-data.htm)

```python
#https://pypi.org/project/edgar/
from edgar import Company
company = Company("Oracle Corp", "0001341439")
tree = company.get_all_filings(filing_type = "10-K")
docs = Company.get_documents(tree, no_of_documents=5)
print(docs)
```

[index](/)
