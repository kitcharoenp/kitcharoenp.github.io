---
layout: post
title: "Quantify Synchrony"
categories: ['DataScience', 'TimeSeries']
---
# Pearson correlation
> The [Pearson correlation][2] measures how two continuous signals co-vary over time and indicate the linear relationship as a number between -1 and 1
* -1 (negatively correlated)
*  0 (not correlated)
*  1 (perfectly correlated).[[1]]

### Cautious
1. Outliers can skew the results of the correlation estimation
2. It assumes the data are [homoscedastic][3] such that the variance of your data is homogenous across the data range.

>In finance, conditional **heteroskedasticity** is often seen in the **prices of stocks and bonds**. The level of volatility of these equities cannot be predicted over any period. [[4]]



[1]: https://towardsdatascience.com/four-ways-to-quantify-synchrony-between-time-series-data-b99136c4a9c9 "Four ways to quantify synchrony "

[2]: https://en.wikipedia.org/wiki/Pearson_correlation_coefficient "Pearson correlation coefficient"

[3]: https://en.wikipedia.org/wiki/Homoscedasticity "Homoscedasticity"

[4]: https://www.investopedia.com/terms/h/heteroskedasticity.asp "Basics of Heteroskedasticity"
