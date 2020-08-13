---
layout: post
title: "Pandas : Basic of DataFrame"
categories: [pandas]
---

```python
#import data
df = pd.DataFrame.from_csv('../data/data.csv')

print(df.head())

Open       High        Low      Close  Adj Close    Volume
Date                                                                       
2014-12-31  46.730000  47.439999  46.450001  46.450001  42.848763  21552500
2015-01-02  46.660000  47.419998  46.540001  46.759998  43.134731  27913900
2015-01-05  46.369999  46.730000  46.250000  46.330002  42.738068  39673900
2015-01-06  46.380001  46.750000  45.540001  45.650002  42.110783  36447900
2015-01-07  45.980000  46.459999  45.490002  46.230000  42.645817  29114100

# Display the size of a DataFrame
df.shape
(780, 6)

# Display the summary statistics of a DataFrame
df.describe()


# Slice row(s) of data

# Selection by label
# select all the price information of data in 2016.
df_2015 = df.loc['2015-01-01':'2015-12-31']


# Selection by position [row index, column index]
df.iloc[0, 0]


# plot only the Close price yearly
plt.figure(figsize=(10, 8))

plt.figure(figsize=(10, 8))
ms['Close'].loc['2015-01-01':'2015-12-31'].plot()
ms['Close'].loc['2016-01-01':'2016-12-31'].plot()
ms['Close'].loc['2017-01-01':'2017-12-31'].plot()
plt.show()

```
