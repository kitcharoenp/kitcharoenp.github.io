---
layout: post
title:  "backtrader: Setup a strategy across a large universe of securities"
categories: [backtrader]
published: true
---

```python
for symbol in symbols:  # where symbols is an iterable with the 200 symbols ...

    cerebro = bt.Cerebro()
    data = bt.fees.MyFeed(symbol)
    cerebro.adddata(data)

    results = cerebro.run()
    # do something with the results
```

The idea behind opening multiple cmd windows and running the strategy for each security was so that the constant live feed that is output by the `logdata, notify_data, notify_order, notify_trade` etc.

[1]: https://www.backtrader.com/blog/posts/2017-04-09-multi-example/multi-example/ "Multi Example"
