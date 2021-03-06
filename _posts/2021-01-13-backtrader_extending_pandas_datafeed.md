---
layout: post
title:  "backtrader: extending  pandas datafeed"
categories: [backtrader]
published: true
---
Extend the existing mechanisms to add extra information in the form of lines [[1]][1]

```python
import backtrader.feeds as btfeeds

class PandasDivergence(btfeeds.PandasData):


    lines = ('div_pos',
             'div_neg',
             'div_at_top',
             'div_at_btm')

    # add the parameter to the parameters inherited from the base class
    #  -1 : autodetect position or case-wise equal name
    #  string : column name (as index) in the pandas dataframe
    params = (('div_pos', -1),
              ('div_neg', -1),
              ('div_at_top', -1),
              ('div_at_btm', -1))

    if False:
        # No longer needed with version 1.9.62.122
        datafields = btfeeds.PandasData.datafields + (
            ['div_pos', 'div_neg', 'div_at_top', 'div_at_btm'])
```

**[samples:][2]**
```python
...
  # Get ohlc data from file
  _df = get_data(ticker,tf='1D')

  # get_macd_divergence() return 'div_pos', 'div_neg', 'div_at_top', 'div_at_btm' columns
  df = get_macd_divergence(_df)

  # Feed data
  datas = PandasDivergence(dataname=df)

  # Add the Data Feed to Cerebro
  cerebro.adddata(datas)
  ...
```

[1]: https://www.backtrader.com/docu/pandas-datafeed/pandas-datafeed/ "Pandas DataFeed"

[2]: https://github.com/mementum/backtrader/blob/master/samples/data-pandas/data-pandas-optix.py "data-pandas-optix"
