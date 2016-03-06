[![npm version](https://badge.fury.io/js/angular2-highcharts.svg)](https://badge.fury.io/js/angular2-highcharts)

# angular2-highcharts
> Angular2 charting components based on Highcharts.

###[Live Demo](http://plnkr.co/edit/pnkc8Iw9cSrCYn9zr0VZ?p=preview)
```
npm install angular2-highcharts --save
```
Tested with Angular 2.0.0-beta.6

## Table of Contents
 - [Usage](#usage)
  - [Basic Usage](#basic-usage)
  - [Handling Events](#handling-events)
    - [Chart Events](#chart-events)
    - [Series Events](#series-events)
    - [Point Events](#point-events)
  - [Dynamic Interaction with Chart Object](#dynamic-interaction-with-chart-object)
  - [Access to the Highcharts Static Members](#access-to-the-highcharts-static-members)
  - [Highstock and Highmaps](#highstock-and-highmaps)
 - [License](#license)

## Usage

### Basic Usage
Main charts functionality provided by the `chart` component and its `options` property.

```TypeScript
import { Component } from 'angular2/core';
import { CHART_DIRECTIVES } from 'angular2-highcharts';

@Component({
    selector: 'simple-chart-example',
    directives: [CHART_DIRECTIVES],
    template: `
        <chart [options]="options"></chart>
    `
})
export class SimpleChartExample {
    constructor() {
        this.options = {
            title : { text : 'simple chart' },
            series: [{
                data: [29.9, 71.5, 106.4, 129.2],
            }]
        };
    }
    options: Object;
}
```
[Live Demo](http://plnkr.co/edit/IuwjpPB1YQW1T7i4B8SZ?p=preview)

### Handling Events
Highcharts itself provides bunch of events, and you still can use them with angular2-higcharts via the `options` property of the `chart` component. But it is not an angular2 way to handle events like this. So that angular2-higcharts provides `EventEmitter<ChartEvent>` wrappers for highcharts events. `ChartEvent` is an angular2-higcharts class which simply wraps original Highcharts events (`chartEvent.originalEvent`) and adds event handler context (`chartEvent.context`) since it differs depending on events.

#### Chart Events 

All the events from the [options.chart.events](http://api.highcharts.com/highcharts#chart.events) are available as output properties of the `chart` component.

```HTML
<chart [options]="options" (selection)="onChartSelection($event)"> </chart>
```
```TypeScript
onChartSelection (e) {
  this.from = e.originalEvent.xAxis[0].min.toFixed(2);
  this.to = e.originalEvent.xAxis[0].max.toFixed(2);
}
```
[Live Demo](http://plnkr.co/edit/vdgKVJOymMYhiyqZrPma?p=preview)
#### Series Events 

To use series events the same way you need to add the `series` component as a child of your chart. The only purpose of this auxiliary component is to provide access to [options.plotOptions.series.events](http://api.highcharts.com/highcharts#plotOptions.series.events) API

```HTML
<chart [options]="options">
    <series (mouseOver)="onSeriesMouseOver($event)">
    </series>
</chart>
<p><b>{{serieName}}</b> is hovered<p>
```
```TypeScript
onSeriesMouseOver (e) {
  this.serieName = e.context.name;
}
```
[Live Demo](http://plnkr.co/edit/GkaJlk2UJjbTwsPyGXGC?p=preview)
#### Point Events 

Similary you can use the `point` to access to [options.plotOptions.series.point.events](http://api.highcharts.com/highcharts#plotOptions.series.point.events) API.
```HTML
<chart [options]="options">
    <series>
        <point (select)="onPointSelect($event)"></point>
    </series>
</chart>
<p><b>{{point}}</b> is selected<p>

```
```TypeScript
onPointSelect (e) {
  this.serieName = e.context.y;
}
```
[Live Demo](http://plnkr.co/edit/TpKoJ60n4vyIDWxHNUkg?p=preview)
### Dynamic Interaction with Chart Object

angular2-higcharts provides possibility to interact with native `HighchartsChartObject` chart object.

```TypeScript 
@Component({
    selector: 'my-app',
    directives: [CHART_DIRECTIVES],
    template: `
      <chart [options]="options" 
             (load)="saveInstance($event.context)">
      </chart>
    `
})
class AppComponent {
    constructor() {
        this.options = {
          chart: { type: 'spline' },
          title: { text : 'dynamic data example'}
          series: [{ data: [2,3,5,8,13] }]
        };
        setInterval(() => this.chart.series[0].addPoint(Math.random() * 10), 1000);
    }
    chart : Object;
    options: Object;
    saveInstance(chartInstance) {
        this.chart = chartInstance;
    }
}
```
[Live Demo](http://plnkr.co/edit/OQSFSKisIIWAH0megy4d?p=preview)
### Access to the Highcharts Static Members
angular2-highcharts exports native `Highcharts` object to interact with its static members.
```TypeScript
import { Highcharts } from 'angular2-highcharts';

Highcharts.setOptions({
  colors: ['#058DC7', '#50B432', '#ED561B']
});
```
[Live Demo](http://plnkr.co/edit/UREjbP8Ij9983H3zJTZs?p=preview)

### Highstock and Highmaps
The `type` property allows you to specify chart type. Possible values are:
* `Chart` (Default value)
* `StockChart`
* `Map` (To use this type you need to load the 'highcharts/modules/map' module additionally.)

```TypeScript
@Component({
    selector: 'stock-chart-example',
    directives: [CHART_DIRECTIVES],
    template: `<chart type="StockChart" [options]="options"></chart>`
})
export class StockChartExample {
    constructor(jsonp : Jsonp) {
        jsonp.request('https://www.highcharts.com/samples/data/jsonp.php?filename=aapl-c.json&callback=JSONP_CALLBACK').subscribe(res => {
            this.options = {
                title : { text : 'AAPL Stock Price' },
                series : [{
                    name : 'AAPL',
                    data : res.json(),
                    tooltip: {
                        valueDecimals: 2
                    }
                }]
            };
        });
    }
    options: Object;
}
```
[Live Demo](http://plnkr.co/edit/2xSewTZ9b213vA0ALmFq?p=preview)

## License
MIT


