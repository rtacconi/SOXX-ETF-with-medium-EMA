```python
import equity_backtesting_library as elib
import datetime as dt

result = {}
result['instrument'] = 'SOXX'
result['spread'] = 0
result['sign1'], result['sign2'] = elib.strategy_type('medium')
result['margin_used'] = 1
result['funds'] = 50000
result['levarage'] = 1
result['tx_cost'] = 0
result['strategy'] = "soxx_medium"
```

## Load data


```python
import equity_backtesting_library as elib
import datetime as dt

result = {}
result['instrument'] = 'SOXX'
result['spread'] = 0
result['sign1'], result['sign2'] = elib.strategy_type('medium')
result['margin_used'] = 1
result['funds'] = 50000
result['levarage'] = 1
result['tx_cost'] = 0
result['strategy'] = "soxx_medium"
stock = elib.get_ticker('SOXX')
print(stock)
stock.info
df = stock.history(period="max")
df = elib.compute_signals(df, result['sign1'], result['sign2'])
del(df['Dividends'])
del(df['Stock Splits'])
del(df['Open'])
del(df['High'])
del(df['Low'])
df.tail()
```

    yfinance.Ticker object <SOXX>





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Close</th>
      <th>Volume</th>
      <th>Return</th>
      <th>sign1</th>
      <th>sign2</th>
      <th>position</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2020-04-20</th>
      <td>226.37</td>
      <td>904400</td>
      <td>-0.021977</td>
      <td>220.653263</td>
      <td>219.809794</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2020-04-21</th>
      <td>215.28</td>
      <td>1217700</td>
      <td>-0.050231</td>
      <td>220.438332</td>
      <td>219.162680</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2020-04-22</th>
      <td>228.31</td>
      <td>1113700</td>
      <td>0.058765</td>
      <td>220.753199</td>
      <td>220.469440</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2020-04-23</th>
      <td>226.03</td>
      <td>977700</td>
      <td>-0.010037</td>
      <td>220.964271</td>
      <td>221.263806</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2020-04-24</th>
      <td>230.73</td>
      <td>962400</td>
      <td>0.020580</td>
      <td>221.354900</td>
      <td>222.616119</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



## Plot of periods of trading


```python
elib.plot_slice(df, '2005-08-08 00:00:00', '2006-01-22 08:13:00')
```


![png](output_4_0.png)



```python
elib.plot_slice(df, '2016-10-08 00:00:00', '2017-01-22 08:13:00')
```


![png](output_5_0.png)



```python
elib.plot_slice(df, '2017-01-02 08:13:00', '2017-01-25 08:13:00')
```


![png](output_6_0.png)



```python
elib.plot_slice(df, '2017-01-03 08:13:00', '2017-01-06 08:13:00')
```


![png](output_7_0.png)



```python
elib.plot_slice(df, '2019-06-10 08:13:00', '2019-10-16 08:13:00')
```


![png](output_8_0.png)



```python
elib.plot_slice(df, '2020-01-01 08:00:00', '2020-04-24 22:13:00')
```


![png](output_9_0.png)


## Calculation of returns


```python
df = elib.long_returns(df, result)
result['total_returns'] = elib.net_returns(
    result['funds'],
    result['margin_used'],
    df,
    result['levarage'])
result['returns'] = elib.inst_returns(
    result['funds'],
    result['margin_used'],
    df,
    result['levarage'])
print('Instrument returns: ', result['returns'])
print('strategy returns: ', result['total_returns'])
result['created_at'] = dt.datetime.utcnow()
print('-----------------------------------------------------')
print("Number of transactions: {}".format(sum(df['position'].diff() != 0)))
result['transaction_cost'] = sum(df['position'].diff() != 0) * \
    result['funds'] * result['margin_used'] * result['levarage'] * result['spread']
#  print("transaction_cost: {}".format(result['transaction_cost']))
print("Transaction cost: {}".format((sum(df['position'].diff() != 0) * 10)))
```

    ('Instrument returns: ', 68083.94595485604)
    ('strategy returns: ', 122485.48105221153)
    -----------------------------------------------------
    Number of transactions: 110
    Transaction cost: 1100



![png](output_11_1.png)

