<template>
  <div>
    <div class="hello" ref="chartdiv">
    </div>
    <div>{{ point }}</div>
  </div>
</template>

<script>
import * as am4core from "@amcharts/amcharts4/core";
import * as am4charts from "@amcharts/amcharts4/charts";
import am4themes_animated from "@amcharts/amcharts4/themes/animated";
import am4lang_ru_RU from "@amcharts/amcharts4/lang/ru_RU";

am4core.useTheme(am4themes_animated);

import result from '/data.json'

export default {
  name: 'HelloWorld',
  data: () => ({
    points: result?.months || [],
    point: null
  }),
  mounted() {
    this.newRender();
  },
  methods: {
    render () {
      let chart = am4core.create(this.$refs.chartdiv, am4charts.XYChart);

      chart.data = [{
        tran_amount: 125.0,
        value_date: new Date("2019-01-05T12:00:00.000")
      }, {
        tran_amount: 12345.0,
        value_date: new Date("2019-02-05T12:00:00.000")
      }, {
        tran_amount: 12345.0,
        value_date: new Date("2019-03-05T12:00:00.000")
      }];
      let categoryAxis = chart.xAxes.push(new am4charts.DateAxis());
      categoryAxis.start = 0.7;
      categoryAxis.keepSelection = true;

      let valueAxis = chart.yAxes.push(new am4charts.ValueAxis());
      let lineSeries = chart.series.push(new am4charts.LineSeries());

      lineSeries.dataFields.valueY = "tran_amount";
      lineSeries.dataFields.dateX = "value_date";

      chart.cursor = new am4charts.XYCursor();
      chart.cursor.behavior = 'panX';
      chart.zoomOutButton.disabled = true;
      chart.cursor.opacity = 0;
      chart.swipeable = true;

      this.chart = chart;
    },
    parseData (arr) {
      return this.points.map(item => {
        return {
          date: this.$moment(item.date?.from).toDate(),
          value: item.average?.single,
          systolic: item.systolic?.single,
          diastolic: item.diastolic?.single,
          open: 70,
          close: 120
        }
      });
      // return []
      // const data = []
      // for(let i = 0; i < 12; i++) {
      //   data.push({
      //     date: new Date(2016, i, 1),
      //     value: Math.round(Math.random() * 50)
      //   })
      // }
      // return data
    },
    newRender () {

      // Themes begin
      am4core.useTheme(am4themes_animated);
      // Themes end

      // Create chart instance
      var chart = am4core.create(this.$refs.chartdiv, am4charts.XYChart);
      // init chart locale 
      chart.language.locale = am4lang_ru_RU;

      console.log(this.parseData());
      chart.data = this.parseData()

      // Set input format for the dates
      // chart.dateFormatter.inputDateFormat = "yyyy-MM-dd";

      chart.events.on('swipeleft', (ev) => {
        console.log('swipe left');
      })

      chart.events.on('swiperight', (ev) => {
        console.log('swipe right');
      })

      // Create axes
      var dateAxis = chart.xAxes.push(new am4charts.DateAxis());
      var valueAxis = chart.yAxes.push(new am4charts.ValueAxis());

      valueAxis.tooltip.disabled = true;
      valueAxis.maximum = 86;
      valueAxis.strictMinMax = true;

      var gridIntervals = [
        // { timeUnit: "millisecond", count: 1 },
        // { timeUnit: "millisecond", count: 5 },
        // { timeUnit: "millisecond", count: 10 },
        // { timeUnit: "millisecond", count: 50 },
        // { timeUnit: "millisecond", count: 100 },
        // { timeUnit: "millisecond", count: 500 },
        // { timeUnit: "second", count: 1 },
        // { timeUnit: "second", count: 5 },
        // { timeUnit: "second", count: 10 },
        // { timeUnit: "second", count: 30 },
        // { timeUnit: "minute", count: 1 },
        // { timeUnit: "minute", count: 5 },
        // { timeUnit: "minute", count: 10 },
        // { timeUnit: "minute", count: 30 },
        // { timeUnit: "hour", count: 1 },
        // { timeUnit: "hour", count: 2 },
        // { timeUnit: "hour", count: 3 },
        // { timeUnit: "hour", count: 6 },
        // { timeUnit: "hour", count: 12 },
        { timeUnit: "day", count: 1 },
        { timeUnit: "day", count: 2 },
        { timeUnit: "day", count: 3 },
        { timeUnit: "day", count: 4 },
        { timeUnit: "day", count: 5 },
        { timeUnit: "month", count: 1 },
        { timeUnit: "month", count: 2 },
        { timeUnit: "month", count: 3 },
        { timeUnit: "month", count: 6 },
        { timeUnit: "month", count: 12 },
        { timeUnit: "year", count: 1 },
        { timeUnit: "year", count: 2 },
        { timeUnit: "year", count: 5 },
        { timeUnit: "year", count: 10 },
        { timeUnit: "year", count: 50 },
        { timeUnit: "year", count: 100 }
      ];
      dateAxis.gridIntervals.setAll(gridIntervals);
      dateAxis.baseInterval = {
        timeUnit: "month",
        count: 1
      };
      // при часоом этот набор
      // dateAxis.renderer.minGridDistance = 75;
      // dateAxis.renderer.grid.template.location = 0.5;
      // dateAxis.startLocation = -0.1;
      // dateAxis.renderer.labels.template.location = 0.5;

      // при месячном это
      dateAxis.dateFormats.setKey("month", "MMMM");
      dateAxis.periodChangeDateFormats.setKey("month", "MMMM");
      dateAxis.renderer.minGridDistance = 50;
      dateAxis.startLocation = 0.4;
      dateAxis.endLocation = 0.6;
      dateAxis.renderer.grid.template.location = 0;
      dateAxis.renderer.labels.template.location = 0;


      // dateAxis.adapter.add("verticalCenter", function() {
      //   console.log('center');
      // });
      // dateAxis.renderer.grid.template.location = 0.5;
      // dateAxis.startLocation = 0.5;
      // dateAxis.endLocation = 0.5;

      // dateAxis.renderer.minGridDistance = 75;
      // dateAxis.renderer.maxGridDistance = 100;
      // dateAxis.renderer.grid.template.location = 0.5;
      // dateAxis.startLocation = 0.1;
      // dateAxis.renderer.labels.template.location = 0.5;
      // dateAxis.baseInterval = {
      //   timeUnit: "1",
      //   count: 'month'
      // };
      
      // убирает дырки
      // dateAxis.skipEmptyPeriods = true;
      // Create value axis break
      // var axisBreak = valueAxis.axisBreaks.create();
      // axisBreak.startValue = 84;
      // axisBreak.endValue = 86;
      // axisBreak.breakSize = 0.05;
      // axisBreak.fill = am4core.color("#396478");

      var range = valueAxis.axisRanges.create();
      range.value = 85;
      range.grid.stroke = am4core.color("#396478");
      range.grid.strokeWidth = 2;
      range.grid.strokeOpacity = 1;

      // Create series
      var series = chart.series.push(new am4charts.LineSeries());
      series.dataFields.valueY = "value";
      series.dataFields.dateX = "date";
      // series.tooltipText = "{value}"
      series.strokeWidth = 2;
      series.minBulletDistance = 15;
      series.dataFields.openValueY = "open";
      series.dataFields.closeValueY = "close";

      // Make bullets grow on hover
      var bullet = series.bullets.push(new am4charts.CircleBullet());
      bullet.circle.strokeWidth = 2;
      bullet.circle.radius = 4;
      bullet.circle.fill = am4core.color("#fff");
      bullet.cursorOverStyle = am4core.MouseCursorStyle.pointer;

      bullet.events.on('hit', (ev) => {
        this.point = ev.target?.dataItem?.dataContext

        chart.series.values.forEach(element => {
          element.strokeOpacity = 0.5
          // element.bullets.values.forEach(bull => {
          //   bull.circle.fill = am4core.color("#fff");
          // })
        });
        bullet.circle.fill = am4core.color("#fff");
        ev.target.circle.fill = am4core.color("#396478");
      })

      chart.events.on('hit', (ev) => {

        //нажимаем на буллет, событие всплывает на график
        if (ev.event?.target?.tagName === 'circle') {
          return
        }
        chart.series.values.forEach(element => {
          element.strokeOpacity = 1
        });
      })

      bullet.adapter.add("hit", function(fill, target) {
        
        console.log(234);
      });

      // ------------show empty data label
      var indicator;
      function showIndicator() {
        if (indicator) {
          indicator.show();
        }
        else {
          indicator = chart.tooltipContainer.createChild(am4core.Container);
          indicator.background.fill = am4core.color("#fff");
          indicator.background.fillOpacity = 0.8;
          indicator.width = am4core.percent(100);
          indicator.height = am4core.percent(100);

          var indicatorLabel = indicator.createChild(am4core.Label);
          indicatorLabel.text = "No data...";
          indicatorLabel.align = "center";
          indicatorLabel.valign = "middle";
          indicatorLabel.fontSize = 20;
        }
      }

      function hideIndicator() {
        indicator.hide();
      }

      chart.events.on("beforevalidated", function(ev) {
        // check if there's data
        console.log(ev.target.data.length);
        if (ev.target.data.length == 0) {
          showIndicator();
        }
        else if (indicator) {
          hideIndicator();
        }
      });
      // ----------------
      // bullethover.properties.cursorOptions = am4core.MouseCursorStyle.grabbing;
      // Make a panning cursor
      chart.cursor = new am4charts.XYCursor();
      chart.cursor.behavior = "panXY";
      // chart.cursor.xAxis = dateAxis;
      chart.cursor.snapToSeries = series;
      chart.cursor.opacity = 0;
      chart.cursor.lineY.opacity = 0;
      chart.cursor.lineX.opacity = 0;
      chart.zoomOutButton.disabled = true;
      chart.maskBullets = false;
      
      dateAxis.keepSelection = true;
      dateAxis.tooltip.disabled = true;
    }
  },
  beforeDestroy() {
    if (this.chart) {
      this.chart.dispose();
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hello {
  width: 100%;
  height: 500px;
}
</style>
