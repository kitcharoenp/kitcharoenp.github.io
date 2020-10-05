---
layout: post
title: "Pandas : Create features and columns"
categories: [pandas]
---
### Price difference
```python
#Create a new column PriceDiff in the DataFrame df
df['PriceDiff'] = df['Close'].shift(-1) - df['Close']
```

### Daily return
```python
df['Return'] = df['PriceDiff'] / df['Close']
```

## List Comprehension

### Direction
```python
df['Direction'] = [1 if df['PriceDiff'].loc[ei] > 0 else 0  for ei in df.index]
```

### Moving average
```python
df['ma9'] = df['Close'].rolling(9).mean()
df['ma26'] = df['Close'].rolling(26).mean()
```

### [Exponentially weighted][1]
```python
df['EMA9'] = df['Close'].ewm(span=9,adjust=False).mean()
df['EMA26'] = df['Close'].ewm(span=26,adjust=False).mean()
```

### Shares
"Shares" column to make decisions base on the strategy

```python
# if EMA9 > EMA26, denote as 1 (long one share of stock), otherwise, denote as 0 (do nothing)

df['Shares'] = [1 if df.loc[ei, 'EMA9']>df.loc[ei, 'EMA26'] else 0 for ei in df.index]
```

### Profit
"Profit" using List Comprehension, for any rows in df, if Shares=1,
the profit is calculated as the close price of tomorrow `df['Close1']`- the close price of today.
Otherwise the profit is 0.
```python
df['Close1'] = df['Close'].shift(-1)
df['Profit'] = [df.loc[ei, 'Close1'] - df.loc[ei, 'Close'] if df.loc[ei, 'Shares']==1 else 0 for ei in df.index]
```

### Wealth
Use `.cumsum()` to calculate the accumulated wealth over the period
```python
df['Wealth'] = df['Profit'].cumsum()
...
df['Wealth'].plot()
plt.title('Total money you win is {}'.format(df.loc[df.index[-2], 'Wealth']))
```


[1]: https://pandas.pydata.org/pandas-docs/stable/user_guide/computation.html#exponentially-weighted-windows "Exponentially weighted windows"
