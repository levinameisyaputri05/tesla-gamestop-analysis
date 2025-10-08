# tesla-gamestop-analysis
Final Coursera Project – Tesla &amp; GameStop Stock Data Analysis

import yfinance as yf
import pandas as pd

# Create a ticker object for Tesla
tesla = yf.Ticker("TSLA")

# Extract historical stock data
tesla_data = tesla.history(period="max")

# Reset the index and display first 5 rows
tesla_data.reset_index(inplace=True)
tesla_data.head()

import requests
from bs4 import BeautifulSoup

# Scrape Tesla revenue data from Macrotrends
url = "https://www.macrotrends.net/stocks/charts/TSLA/tesla/revenue"
html_data = requests.get(url).text
soup = BeautifulSoup(html_data, "html.parser")

# Extract the revenue table
tables = soup.find_all("table")
for table in tables:
    if "Tesla Quarterly Revenue" in table.text:
        tesla_revenue = pd.read_html(str(table))[0]
        break

# Clean data
tesla_revenue.columns = ['Date', 'Revenue']
tesla_revenue['Revenue'] = tesla_revenue['Revenue'].str.replace(r'[\$,]', '', regex=True)
tesla_revenue.dropna(inplace=True)

# Show last 5 rows
tesla_revenue.tail()


# Create a ticker object for GameStop
gme = yf.Ticker("GME")

# Extract historical stock data
gme_data = gme.history(period="max")

# Reset the index and display first 5 rows
gme_data.reset_index(inplace=True)
gme_data.head()


url = "https://www.macrotrends.net/stocks/charts/GME/gamestop/revenue"
html_data = requests.get(url).text
soup = BeautifulSoup(html_data, "html.parser")

# Extract revenue table
tables = soup.find_all("table")
for table in tables:
    if "GameStop Quarterly Revenue" in table.text:
        gme_revenue = pd.read_html(str(table))[0]
        break

# Clean data
gme_revenue.columns = ['Date', 'Revenue']
gme_revenue['Revenue'] = gme_revenue['Revenue'].str.replace(r'[\$,]', '', regex=True)
gme_revenue.dropna(inplace=True)

# Show last 5 rows
gme_revenue.tail()


import plotly.graph_objects as go

def make_graph(stock_data, title):
    fig = go.Figure()
    fig.add_trace(go.Scatter(x=stock_data['Date'], y=stock_data['Close'], name="Stock Price"))
    fig.update_layout(title=title, xaxis_title="Date", yaxis_title="Price (USD)")
    fig.show()

# Plot Tesla graph
make_graph(tesla_data, "Tesla Stock Price Over Time")


make_graph(gme_data, "GameStop Stock Price Over Time")

