---
layout: post
title: "Ionic : Amcharts 4 TimeLine"
published: false
categories: [ionic]
---
### ionic generate page

Generate am4chart-timeline page
```
$ ionic generate page am4chart-timeline --no-module
[OK] Generated a page named am4chart-timeline!
```

### [Install Amcharts4 via NPM][0]

```
$ npm install @amcharts/amcharts4 --save
```


[Amcharts 4][1]
### Importing modules/scripts
### Creating chart instance
###

### Fixed

```
let categoryAxis = chart.yAxes.push(new am4charts.CategoryAxis());

# Raise error
[app-scripts] [03:54:00]  typescript: src/pages/am4chart-timeline/am4chart-timeline.ts, line: 172
[app-scripts]             Argument of type 'CategoryAxis<AxisRenderer>' is not assignable to parameter of type
[app-scripts]             'Axis<AxisRendererCurveY>'. Types of property '_renderer' are incompatible. Type 'AxisRenderer' is not
[app-scripts]             assignable to type 'AxisRendererCurveY'. Property 'axisRendererX' is missing in type 'AxisRenderer'.
[app-scripts]      L172:      let categoryAxis = chart.yAxes.push(new am4charts.CategoryAxis());
[app-scripts]      L173:      categoryAxis.dataFields.category = "category";
```
Solution
```
let categoryAxis = chart.yAxes.push(new am4charts.CategoryAxis() as any);
```

[0]: https://www.amcharts.com/download/ "Amcharts4 Download"
[1]: https://www.amcharts.com/docs/v4/chart-types/timeline/ "Amcharts4 TimeLine Chart"
