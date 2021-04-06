# <a name="id0"></a>Algorithmic trading with Python 

### CONTENT
### [1 - Starting point](#id1)
### [2 - Data analysis](#id2)
#### [2a - Data Preparation](#id2a)
#### [2b - Web Application](#id2b)

### -----------------------------------------------------------------------------------------------------------------------------
### <a name="id1"></a>1 - Starting point [(Back to the Top)](#id0)

After I have had a first look into the topic of analyzing ETF with Python (i.e. I got some basic understanding on potential ETF data sources, I conducted some basic data analysis and visualization of ETF price, return and volatility developments over time, I had an initial look into the aspect of portfolio optimisation (i.e. in terms of risk-efficiency) and I finally displayed the analysis results and provided some user interactivity by means of a Streamlit web application) I came across the topic of algorthimic trading. 

In contrast analyzing ETF where you are looking at an index (i.e. bundle) of stocks, e.g. exchange traded funds such as the MSCI World, MDAX, etc., in algorthmic trading the focus is more towards analyzing the price development of single stocks and comparing these developments across different stocks. Typical questions of interest in this case are e.g. which stocks have a high intrinsic value (e.g. in terms of (future) earnings, EBITDA, etc.) compared to the current stock price level (thereby indicating that the current stock prices is undervalued and might increase in the future)? How can we compare stocks in terms of their recent stock price developments and do we observe some kind of momentum within the stock price developments which might be considered as an indication/signal with regards to the future stock price movement. Based on such ideas one could try to define certain KPI (key performance indicators) which would form the basis for somekind of automated trading strategy. E.g. a value based trading strategy could focus on those stocks (within a pre-defined set of stocks) which have the lowest price to earings or price to EBITDA ratio. On the other hand a simple momentum based trading strategy could build upon some measures regarding recent stock price returns (e.g. last 1 year, 6 months, 3 months, etc.) or a comparison of the current stock price with the highest and lowest stock price during e.g. the last year(s). Obviously one could also think about combining a value and momentum trading strategy by using some kind of combined KPI which consistes of values based and momentum based KPI mereged together according to some reasonable weighting/rule set.

To make it simple and give an explicit example: While the ETF analysis was looking at the price, return and volatility developments of e.g. the S&P 500 index we are now looking at the stock price development of each and evey ticker within the S&P 500. Thereby we try to compare these stocks with each other in order to derive and provide the basis for some kind of algorthmic trading stragegy.




financial institutions are now evolving to technology companies rather than only staying occupied with just the financial aspect: besides the fact that technology brings about innovation the speeds and can help to gain a competitive advantage, the rate and frequency of financial transactions, together with the large data volumes, makes that financial institutions’ attention for technology has increased over the years and that technology has indeed become the main enabler in finance.

Among the hottest programming languages for finance, you’ll find R and Python, alongside languages such as C++, C#, and Java. In this tutorial, you’ll learn how to get started with Python for finance. The tutorial will cover the following:



Next data science area which I was interested in related to the topic of finance. When it comes to investing money many advices center around investments into ETF (i.e. exchange traded funds vs. e.g. single stocks). Therefore I wanted to get a better understanding on the variety of ETFs and their historical developments. How did the prices of different ETF develop in recent years? Which ETF had a large (annual) return over a given time period? How volatile were these returns? What is the ratio between return and volatility for a given ETF. Is there a strong positive/negative correlation amongst the returns of different ETF? 

Again, like in any data science project the first step centers around data: Where do I get appropriate data - ideally in a very automated way and always up to date? Once an interface to the relevant data source is available much work has to be conducted around the topic of analyzing this data, i.e. data preparation, applying statistical/econometric methods, presentation and visualization of results, interpretation, etc..

### <a name="id2"></a>2 - Data analysis [(Back to the Top)](#id0)

The following python scripts contain the various steps of the data preparation (step 1 & 2) and analysis (step 3) which have been conducted: 

- [Data Preparation (step 1) – Select a large ETF Universe](01_select_large_EFT_universe.py)
- [Data Preparation (step 2) - Generate data for selected ETFs](02_generate_ETF_universe_data_v1.py)
- [Analyze Data (step 3) - Construct a simple interactive platform to analyze/visualize selected ETFs](03_analyse_ETF.py)

A further and more detailed description of these python script is given below.

#### <a name="id32"></a>2a - Data Preparation [(Back to the Top)](#id0)

As always, the initial step is about getting data concerning the topic of interest, in this case ETF. When searching in the internet you find a large variety of potential libraries which offer an interface to financial data. E.g. one source is the library [pandas-datareader](https://pandas-datareader.readthedocs.io/en/latest/index.html) which offers access to various (financial) data sources. After some search in Google I decided to use the package [investpy](https://investpy.readthedocs.io/index.html) due to its documentation which I found helpful as well as the easy access to a wide range of ETFs in this library. According to its documentation the library investpy retrieves data from the finance portal [investing.com](https://www.investing.com/). After having installed investpy in the usual manner you can import the library and use the various functionalities to retrieve recent and historical data from indexed financial products. 

In a first step I would like to draw the attention to the following function [investpy.etfs.search_etfs(by, value)](https://investpy.readthedocs.io/_api/etfs.html?) which allows to search relevant ETF by components of its name. In that regards it might be good to know that the name of an ETF provides a large amount of information, such as issuing company (e.g. iShares, Xtrackers, etc.), index name (e.g. MSCI World, S&P 500, DAX, etc.) and regulatory aspects (e.g. UCITS) etc.. For a good explanation on this topic look for example [here](https://www.justetf.com/de/news/etf/wie-sie-etf-namen-einfach-entschluesseln.html). As you can see in the program my initial goal was to get an extract/universe of ETF which are issued by Blackrock and hence were named iShares. Moreover, I added some further attributes describing the ETF investment strategy (e.g. type of index, region, etc.). This information could also easily be obtained by scanning the ETF name for different words.

```
import investpy
import pandas as pd
from pandas import DataFrame

locpath1 = "C:/Users/Marc Wellner/01_projects/streamlit/02_finance_app/01_data/"

# Select iShare ETF
etf_univsel = investpy.etfs.search_etfs("name", "iShares")

pd.set_option('display.max_columns', 100)
print(etf_univsel)

# Define furhter attributes which describe the investment strategy of the ETF
etf_univsel.loc[etf_univsel['name'].str.contains('Core'),'etf_base']='Core'
etf_univsel.loc[etf_univsel['name'].str.contains('Prime'),'etf_base']='Prime'

etf_univsel.loc[etf_univsel['name'].str.contains('DAX'),'etf_index']='DAX'
etf_univsel.loc[etf_univsel['name'].str.contains('MDAX'),'etf_index']='MDAX'
etf_univsel.loc[etf_univsel['name'].str.contains('SDAX'),'etf_index']='SDAX'
etf_univsel.loc[etf_univsel['name'].str.contains('EURO STOXX'),'etf_index']='EURO STOXX'
etf_univsel.loc[etf_univsel['name'].str.contains('MSCI'),'etf_index']='MSCI'
etf_univsel.loc[etf_univsel['name'].str.contains('S&P'),'etf_index']='S&P'
etf_univsel.loc[etf_univsel['name'].str.contains('NASDAQ'),'etf_index']='NASDAQ'
etf_univsel.loc[etf_univsel['name'].str.contains('Dow Jones'),'etf_index']='Dow Jones'

etf_univsel.loc[etf_univsel['name'].str.contains('DAX'),'etf_region']='DE'
etf_univsel.loc[etf_univsel['name'].str.contains('MDAX'),'etf_region']='DE'
etf_univsel.loc[etf_univsel['name'].str.contains('SDAX'),'etf_region']='DE'
etf_univsel.loc[etf_univsel['name'].str.contains('EURO'),'etf_region']='Euro'
etf_univsel.loc[etf_univsel['name'].str.contains('Euro'),'etf_region']='Euro'
etf_univsel.loc[etf_univsel['name'].str.contains('Europe'),'etf_region']='Europe'
etf_univsel.loc[etf_univsel['name'].str.contains('Asia'),'etf_region']='Asia'
etf_univsel.loc[etf_univsel['name'].str.contains('China'),'etf_region']='China'
etf_univsel.loc[etf_univsel['name'].str.contains('USA'),'etf_region']='USA'
etf_univsel.loc[etf_univsel['name'].str.contains('World'),'etf_region']='World'

etf_univsel.loc[etf_univsel['name'].str.contains('UCITS'),'etf_ucits']='UCITS'

etf_univsel.loc[etf_univsel['name'].str.contains('MSCI World UCITS'),'etf_cat']='etf_worldbase'

# Export to xls
etf_univsel.to_excel(locpath1+"etf_univsel.xlsx", sheet_name='Tabelle1')
```

After having extracted this very broad datasheet of almost 2.000 different ETF from iShares which was then stored in an Excel file called etf_univsel.xlsx I did some further manual research obviously based on own ideas regarding an appropriate investment strategy (e.g. with regards to index, region, sectors, etc.). This process then led to a selection of 8 ETF which I wanted to analyze further and in more detail. These 8 ETF focus on equities (e.g. instead of bonds) and differentiate somehow by region, company size and sector (e.g. technology). Following ETF were selected:
