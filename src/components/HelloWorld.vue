<template>
  <div>
    <div class="hello" ref="chartdiv">
    </div>
    <div><b>{{ getFormattedDate }}</b></div>
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
    points: result?.minutes || [],
    point: null,
    clickedBullets: [],
    lastDate: null
  }),
  mounted() {
    this.newRender();
  },
  computed: {
    getFormattedDate() {
      return this.$moment(this.point).format('yyyy-MM-DD HH:mm:ss')
    }
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
      chart.hiddenState.properties.opacity = 0; // this creates initial fade-in
      // init chart locale 
      chart.language.locale = am4lang_ru_RU;

      chart.data = this.parseData()

      // Set input format for the dates
      // chart.dateFormatter.inputDateFormat = "yyyy-MM-dd";
      let cursorPosition = {
        x: null,
        y: null
      };

      // chart.events.on('wheelleft', (ev) => {
      //   // console.log('swipe left');
        
      //   console.log(ev);
      //   return false
      // })

      

      // chart.events.on('swipeleft', (ev) => {
      //   // console.log('swipe left');
      //   let xAxis = ev.target.xAxes.getIndex(0);
      //   let yAxis = ev.target.yAxes.getIndex(0);
      //   cursorPosition.x = xAxis.positionToDate(xAxis.toAxisPosition(ev.target.cursor.xPosition));
      //   cursorPosition.y = yAxis.positionToValue(yAxis.toAxisPosition(ev.target.cursor.yPosition));

      //   console.log(cursorPosition);
      // })

      // chart.events.on('swiperight', (ev) => {
      //   let xAxis = ev.target.xAxes.getIndex(0);
      //   let yAxis = ev.target.yAxes.getIndex(0);
      //   cursorPosition.x = xAxis.positionToDate(xAxis.toAxisPosition(ev.target.cursor.xPosition));
      //   cursorPosition.y = yAxis.positionToValue(yAxis.toAxisPosition(ev.target.cursor.yPosition));

      //   console.log(cursorPosition);
      // })

      // Create axes
      var dateAxis = chart.xAxes.push(new am4charts.DateAxis());
      var valueAxis = chart.yAxes.push(new am4charts.ValueAxis());

      valueAxis.tooltip.disabled = true;
      valueAxis.extraMax = 0.00001
      valueAxis.extraMin = 0.01
      valueAxis.start = 0
      // valueAxis.min = 0;
      valueAxis.renderer.gridContainer.zIndex = 1;
      valueAxis.cursorTooltipEnabled = false;

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
        { timeUnit: "minute", count: 5 },
        // { timeUnit: "minute", count: 10 },
        // { timeUnit: "minute", count: 30 },
        // { timeUnit: "hour", count: 1 },
        // { timeUnit: "hour", count: 2 },
        // { timeUnit: "hour", count: 3 },
        // { timeUnit: "hour", count: 6 },
        // { timeUnit: "hour", count: 12 },
        // { timeUnit: "day", count: 1 },
        // { timeUnit: "day", count: 2 },
        // { timeUnit: "day", count: 3 },
        // { timeUnit: "day", count: 4 },
        // { timeUnit: "day", count: 5 },
        // { timeUnit: "month", count: 1 },
        // { timeUnit: "month", count: 2 },
        // { timeUnit: "month", count: 3 },
        // { timeUnit: "month", count: 6 },
        // { timeUnit: "month", count: 12 },
        // { timeUnit: "year", count: 1 },
        // { timeUnit: "year", count: 2 },
        // { timeUnit: "year", count: 5 },
        // { timeUnit: "year", count: 10 },
        // { timeUnit: "year", count: 50 },
        // { timeUnit: "year", count: 100 }
      ];
      dateAxis.gridIntervals.setAll(gridIntervals);
      dateAxis.baseInterval = {
        timeUnit: "minute",
        count: 5
      };
      // при часоом этот набор
      // dateAxis.dateFormats.setKey("hour", "HH:mm");
      // dateAxis.periodChangeDateFormats.setKey("hour", "HH:mm");
      dateAxis.renderer.minGridDistance = 75;
      dateAxis.renderer.grid.template.location = 0;
      dateAxis.startLocation = -0.1;
      dateAxis.start = 0.95;
      // dateAxis.end = 0.3;
      dateAxis.renderer.labels.template.location = 0;

      dateAxis.events.on('rangechangeended', (ev) => {
        // console.log(
        //   this.$moment(ev.target.minZoomed).format('yyyy-MM-DD HH:mm:ss'),
        //   this.$moment(ev.target.maxZoomed).format('yyyy-MM-DD HH:mm:ss')
        // );

        var startDate = this.$moment(ev.target.minZoomed);
        var endDate = this.$moment(ev.target.maxZoomed);
        // var newDate = moment.duration(endTime.diff(startTime)).asDate
        //   console.log(newDate)
        // )
        const diffStartEndMinutes = this.$moment.duration(endDate.diff(startDate)).asMinutes() / 2
        const centerDate = endDate.clone().subtract(diffStartEndMinutes, 'minutes')
        const startOfCenterDate = centerDate.startOf('day')

        console.log(startOfCenterDate);

        if (this.$moment(this.point).isSameOrBefore()) {
          this.point = startOfCenterDate
        }

        // console.log(endDate.clone().subtract(minutes, 'minutes'))
      })
      // Create axis ranges for weekends
      // dateAxis.events.on("datavalidated", (ev) => {
      //   const axis = ev.target;
      //   const start = this.$moment(axis.positionToDate(0));
      //   const end = this.$moment(axis.positionToDate(1));
      //   console.log(start, end);

      //   // Get start date
      //   let current = start.clone().minutes(0).seconds(0);

      //   while(current.isBefore(end)) {
      //     if (current.hours() == 0 && current.minutes() == 0) {
      //       // Create a range
      //       var range = axis.axisRanges.create();
      //       range.date = current.toDate();
      //       range.endDate = current.clone().add('minute', 1).toDate();
      //       range.axisFill.fill = am4core.color("#396478");
      //       range.axisFill.fillOpacity = 0.2;
      //       range.grid.strokeOpacity = 0;
      //     }
      //     current.add('hour', 1)
      //   }
        
      // });
      // при месячном это
      // dateAxis.gridIntervals.setAll(gridIntervals);
      // dateAxis.baseInterval = {
      //   timeUnit: "month",
      //   count: 1
      // };
      // dateAxis.dateFormats.setKey("month", "MMMM");
      // dateAxis.periodChangeDateFormats.setKey("month", "MMMM");
      // dateAxis.renderer.minGridDistance = 50;
      // dateAxis.startLocation = 0.4;
      // dateAxis.endLocation = 0.6;
      // dateAxis.renderer.grid.template.location = 0;
      // dateAxis.renderer.labels.template.location = 0;

      dateAxis.keepSelection = true;
      dateAxis.tooltip.disabled = true;

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

      // var range = valueAxis.axisRanges.create();
      // range.value = 85;
      // range.grid.stroke = am4core.color("#396478");
      // range.grid.strokeWidth = 2;
      // range.grid.strokeOpacity = 1;

      var clickedBullets = []

      const createSeries = (valueProp) => {
        // Create series
        var series = chart.series.push(new am4charts.LineSeries());
        series.dataFields.valueY = valueProp;
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
          ev.event.stopPropagation()
          this.point = ev.target?.dataItem?.dataContext?.date

          chart.series.values.forEach(element => {
            element.strokeOpacity = 0.5
          });
          
          // reset styles circles
          clickedBullets.forEach(item => {
            item.fill = am4core.color("#fff")
            item.scale = 1
          })
          clickedBullets = []
          // init circles styles
          ev.target.circle.fill = am4core.color("#67b7dc");
          ev.target.circle.scale = 1.3
          // ev.target.circle.radius = 2;

          clickedBullets.push(ev.target.circle)
        })
      }

      createSeries('value')

      chart.events.on('hit', (ev) => {
        if (ev.event.cancelBubble) {
          return
        }
        // reset styles circles
        clickedBullets.forEach(item => {
          item.fill = am4core.color("#fff")
          item.scale = 1
        })
        clickedBullets = []

        chart.series.values.forEach(element => {
          element.strokeOpacity = 1
        });
      })

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

      chart.events.on("beforevalidated", (ev) => {
        
        // check if there's data
        if (ev.target.data.length == 0) {
          showIndicator();
        }
        else if (indicator) {
          hideIndicator();
        } else {
          this.point = ev.target.data?.[ev.target.data.length - 1].date
        }
      });
      // ----------------
      // bullethover.properties.cursorOptions = am4core.MouseCursorStyle.grabbing;
      // Make a panning cursor
      const cursor = new am4charts.XYCursor();
      cursor.behavior = "panX";
      chart.cursor = cursor
      // chart.cursor.xAxis = dateAxis;
      // chart.cursor.snapToSeries = series;
      cursor.lineX.disabled = true;
      chart.cursor.opacity = 0;
      chart.cursor.lineY.opacity = 0;
      chart.cursor.lineX.opacity = 0;
      chart.scrollbarX = new am4core.Scrollbar();
      // chart.scrollbarX.events.on('wheel', (ev) => {
      //   console.log(ev);
      // })
      chart.scrollbarX.disabled = true;
      chart.zoomOutButton.disabled = true;
      // chart.swipeable = true;
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
