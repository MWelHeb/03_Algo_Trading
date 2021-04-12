<p align="right"> <a href="https://github.com/MWelHeb/empirical_eyes/blob/master/README.md">Back to emprical eyes</a> </p>

<img src = "algo_trading.jfif" width="250"><img src = "algo_trading_2.jfif" width="200"><img src = "algo_trading_3.jfif" width="300">

# <a name="id0"></a>Algorithmic trading with Python 

### CONTENT
### [1 - Starting point](#id1)
### [2 - Data analysis](#id2)
#### [2a - Data Preparation](#id2a)
#### [2b - Web Application](#id2b)

### -----------------------------------------------------------------------------------------------------------------------------
### <a name="id1"></a>1 - Starting point [(Back to the Top)](#id0)

During my first finance related Python project, which was about the topic of [analyzing ETF with Python](https://github.com/MWelHeb/02_ETF_Analysis/blob/main/ETF_Analysis.md) I came across the theme of algorithmic trading. 

Algorithmic trading, i.e., trading based on algorithms or rules (which are developed based on some trading strategy), refers to computerized, automated trading of financial instruments like stocks with little or no human intervention during trading hours. Nowadays in the U.S. stock market and many other developed financial markets, about 70-80 percent of overall trading volume is generated through algorithmic trading. Yet, in emerging economies such as India, the overall trading volume of algorithmic trading is still estimated to be lower at a level of ~40%. Obviously, these figures are impressive and hence the topic caught my attention and I thought that it could be a nice use case of applying and practicing Python as well quantitative methods.

Likewise, to the analysis of ETF I'm now also looking at the analysis of some financial instrument but in contrast to analyzing the movements of an index (i.e. bundle) of stocks (e.g. exchange traded funds such as the MSCI World, MDAX, etc.) in this algorithmic trading project the focus is more towards analyzing the price developments of single stocks and comparing these developments across different stocks. Typical questions of interest in this case could be e.g.: 

- Which stocks have a high (intrinsic) value (e.g., in terms of future earnings, EBITDA, etc.) compared to the current stock price level, thereby indicating that the current stock price is maybe undervalued and might increase in the future? 
- How can we compare stocks in terms of their recent stock price development and do we observe some kind of momentum within the stock price developments which might be considered as an indication/signal with regards to future stock price movements? 

Based on such ideas one could try to define certain KPIs (key performance indicators) which would form the basis for some kind of trading strategy (which can then be automated). E.g., a value based trading strategy could focus on those stocks (within a pre-defined set of stocks) which have the lowest price to earnings ratio (or as an alternative e.g. price to EBITDA ratio). On the other hand, a simple momentum based trading strategy could build upon some measures regarding recent stock price returns (e.g. over the last 1 year, 6 months, 3 months, etc.) or maybe also on a comparison of the current stock prices with the highest and lowest stock prices during e.g. the last year(s)/month(s). Obviously, one could also think about a combination of a value and momentum based trading strategy by using a combined set of KPIs which consists of value and momentum based KPIs merged together according to some reasonable weighting or rule set.

To make it simple and provide an explicit case study/example: While the ETF analysis was looking at the price, return and volatility developments of e.g. the S&P 500 index we are now looking at the stock price development of each and every ticker within the S&P 500. Thereby we try to compare these stocks with each other in order to derive and provide the basis for some kind of algorithmic trading strategy.

Fortunately, nowadays the barriers to get started with algorithmic trading are rather low:

- The internet and literature provide a large and mostly free source of open information and knowledge on any finance related topic and theory as well as know how concerning any the required quantitative method.
- Software which allows to access and process financial data as well as to program and test an assumed trading algorithm is available for free - basically every piece of software that is needed is available in the form of open source; specifically, Python has become the language and ecosystem of choice
- Open data sources: More and more valuable online financial data sources are open and for free. These platforms mostly provide an easy, standardized access to historical and even real-time data (via APIs)  

Again, like in any data science project the first step centers around data: Where do I get appropriate data - ideally in a very automated way and always up to date? Once an interface to the relevant data source is available much work has to be conducted around the topic of analyzing this data, i.e., data preparation, applying statistical/econometric methods, presentation and visualization of results, interpretation, etc..

### <a name="id2"></a>2 - Data analysis [(Back to the Top)](#id0)

The following python scripts contain the various steps of the data preparation (step 1) and analysis (step 2) which have been conducted: 

- [Data Preparation (step 1) – Get financial data from IEX Cloud](xxx.py)
- [Analyze Data (step 2) - Construct KPI for a value and momentum based valuation](xxx.py)

A further and more detailed description of these python script is given below.

#### <a name="id32"></a>2a - Data Preparation [(Back to the Top)](#id0)

As always, the initial step is about getting data concerning the topic of interest. As mentioned above we are interested in potentially all stocks which are in the S&P 500 and hence we first need to get a list of all these tickers. Apart from importing the typical packages which are needed for the further analysis the coding below provides this extracts from Wikipedia which contains the list of S&P 500 companies with further information e.g. concerning ticker, sector, headquarter, etc.. The extract is then stored in a csv file. 

```
import numpy as np #The Numpy numerical computing library
import pandas as pd #The Pandas data science library
import requests #The requests library for HTTP requests in Python
import math #The Python math module
#import iexfinance
from scipy import stats #The SciPy stats module

#Get list of all stocks from S&P 500
#https://medium.com/wealthy-bytes/5-lines-of-python-to-automate-getting-the-s-p-500-95a632e5e567
#read_html returns a list of dataframe objects -> in this case two objects where we only need
#the first one. Therefore we select index 0

table=pd.read_html('https://en.wikipedia.org/wiki/List_of_S%26P_500_companies')
df = table[0]
df.to_csv('S&P500-Info.csv')
df.to_csv("S&P500-Symbols.csv", columns=['Symbol'])

stocks = pd.read_csv('S&P500-Info.csv')
```

Next step is related to finding a financial API which provides free stock market data (in our case for the S&P 500). Searching the internet will provide various alternatives, see e.g. the following recent article ["Best 5 free stock market APIs in 2020"](https://towardsdatascience.com/best-5-free-stock-market-apis-in-2019-ad91dddec984). For this use case I have decided to go with [IEX Cloud](https://iexcloud.io/) which is the data provider subsidiary of the IEX stock exchange. Having looked at many financial data providers IEX Cloud seemed to me very attractive in terms of high-quality data at an affordable price. For this case study I started with using the free platform account which contains a limited amount of 50,000 credits. Since the credits are reduced according to a pay-per-use principle it requires you to be careful with extracting data when developing the Python program. Here the sandbox environment comes into play. While API calls which are made to the production environment require the use of credits from your IEX Cloud plan, sandbox testing is free of charge and allows an unlimited amount of API calls. Note however, that the IEX Cloud sandbox only returns randomized test data which is meant to mimic the results returned from the production API. 

After having signed up for IEX Cloud you should have received your API token and should now be able to run the following code. First you need to define whether you intend to retrieve data from the sandbox or the production environment. Depending on this decision you select a different API token and a different base URL for the API requests. After this the next step consists of defining an empty pandas dataframe in the desired structure (i.e. which attributes/metrics for the companies do we want to retrieve from the IEX Cloud API). Then we will use the package request to do the respective HTTP requests whereby the URL will be different and dynamic depending on (a) the IEX Cloud endpoints (e.g. company, quote, stats, etc.) (b) the company tickers (i.e. symbol) but also (c) the base URL and the token (production vs sandbox environment). As a result the IEX Cloud API returns JSON objects in response to HTTP requests which is then (a) selectively retrieved, according to the required metrics (e.g. CompanyName, latestPrice, etc. ), (b) fed into a pandas series which itself (c) appends to the initially defined dataframe. Once the loop over all S&P500 tickers has been finalized the dataframe is fully loaded with actual information about all S&P 500 companies and this result is then stored into an xlsx file. 

```
###### IEX Parameters 
# Get the relevant Token 
from secrets import IEX_CLOUD_API_TOKEN
token = IEX_CLOUD_API_TOKEN

#from secrets import IEX_SANDBOX_API_TOKEN
#token = IEX_SANDBOX_API_TOKEN

# Get the relevant BASE URL
#Production: https://cloud.iexapis.com/stable
#Sandbox: https://sandbox.iexapis.com/stable
#base_url = 'https://sandbox.iexapis.com/stable'
base_url = 'https://cloud.iexapis.com/stable'

###### Define a final dataframe 
my_col = [    'Ticker',                             #1
              'Company Name',                       #2
              'Industry',                           #3
              'Price',                              #4
              'Adjusted 52 week low',               #5
              'Adjusted 52 week high',              #6
              'Market Capitalization',              #7
              'Twelve months Earnings per Share',   #8
              'Price to Earnings Ratio',            #9
              'One-Year Price Return',              #10
              'Six-Month Price Return',             #11
              'Three-Month Price Return',           #12
              'One-Month Price Return',             #13
               ]

fin_df = pd.DataFrame(columns = my_col)
fin_df

###### Extract data from IEX API for all S&P 500 ticker and fill into final dataframe for further analysis 
#resp = requests.get(base_url + '/status')
for ind in stocks.index:
    print(ind, stocks['Symbol'][ind])
    url_company = f"{base_url}/stock/{stocks['Symbol'][ind]}/company?token={token}"   
    data_company = requests.get(url_company).json()
    
    url_quote  = f"{base_url}/stock/{stocks['Symbol'][ind]}/quote?token={token}"
    data_quote = requests.get(url_quote).json()
    
    url_stats  = f"{base_url}/stock/{stocks['Symbol'][ind]}/stats?token={token}"
    data_stats = requests.get(url_stats).json()
    
    #url_advstats  = f"{base_url}/stock/{stocks['Symbol'][ind]}/advanced-stats?token={token}"
    #data_advstats = requests.get(url_advstats).json()
    
    
    fin_df = fin_df.append(pd.Series( [data_quote['symbol'],                   #1
                                       data_quote['companyName'],              #2
                                       data_company['industry'],               #3
                                       data_quote['latestPrice'],              #4
                                       data_quote['week52Low'],                #5
                                       data_quote['week52High'],               #6
                                       data_quote['marketCap'],                #7
                                       data_stats['ttmEPS'],                   #8
                                       data_quote['peRatio'],                  #9
                                       data_stats['year1ChangePercent'],       #10
                                       data_stats['month6ChangePercent'],      #11
                                       data_stats['month3ChangePercent'],      #12 
                                       data_stats['month1ChangePercent'],      #13
                                      ],
                                                    index = my_col), 
                                        ignore_index = True)

fin_df

###### Store raw data extract in to xls.
locpath1 = "C:/xxxxxx/01_projects/jupyterlab/03_algorithmic_trading/"
fin_df.to_excel(locpath1+"fin_df.xlsx", sheet_name='Tabelle1')    

```
In this context I would like to emphasize that IEX Cloud also offers so-called batch API calls which make the code far more performant and usually are also more efficient in terms of using less credits. Unfortunately however, the free account of IEX Cloud does not allow for batch requests. Moreover, I would like to state that while there isn’t an official Python library for the IEX API, there are several that have been created to make the API easier to interact with. Amongst others the two recommended Python libraries are [iexfinance]() and [pyEX]() and they offer capabilities such as the automatic creation of a pandas dataframe. However, in our case here I decided not to spend a lot of time getting familiar and using such a library but rely on the generic and native library request to access the IEX API.
