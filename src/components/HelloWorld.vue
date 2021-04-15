<template>
  <div class="main">
    <div class="hello" ref="chartdiv">
    </div>
    <div><b>{{ getFormattedDate }}</b></div>
    <div style="margin-top: 20px; font-size:25px; color: red"><b>{{ getFormattedDateNew }}</b></div>
    <!-- <a href="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGQAAABkCAYAAABw4pVUAAAAnElEQVR42u3RAQ0AAAgDIE1u9FvDOahAJzXFGS1ECEKEIEQIQoQgRIgQIQgRghAhCBGCECEIQYgQhAhBiBCECEEIQoQgRAhChCBECEIQIgQhQhAiBCFCEIIQIQgRghAhCBGCEIQIQYgQhAhBiBCEIEQIQoQgRAhChCAEIUIQIgQhQhAiBCEIEYIQIQgRghAhCBEiRAhChCBECEK+W0L3+TnU7375AAAAAElFTkSuQmCC"
      download="myImage.png"
      target="_blank"
    >
      Download
    </a> -->
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
    points: result?.['test2'] || [],
    pointsNew: result?.['test'] || [],
    barPoints: result?.['pulse'] || [],
    point: null,
    clickedBullets: [],
    lastDate: null,
    allBulets: []
  }),
  mounted() {
    this.newRender();
  },
  computed: {
    getFormattedDate: {
      get () {
        return this.$moment(this.point).format('yyyy-MM-DD')
      }
    },
    getFormattedDateNew: {
      get () {
        return this.$moment(this.point).format('yyyy-MM-DD HH:mm:ss')
      }
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
    parseNewDate(arr) {
      return this.pointsNew.map(item => {
        return {
          date: this.$moment(item.date?.from).toDate(),
          value: item.average?.single,
          systolic: item.systolic?.single,
          diastolic: item.diastolic?.single,
          open: 70,
          close: 120
        }
      });
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

      function randomInteger(min, max) {
        return Math.floor(Math.random() * (max - min + 1)) + min;
      }

      // genearte all days of month
      // const data = []
      // for(let i = 1; i < 31; i++) {
      //   const val = randomInteger(70, 160)
      //   const hours = randomInteger(0, 23)
      //   console.log(val, hours)

      //   data.push({
      //     date: new Date(2020, 2, i, hours, 4),
      //     value: val
      //   })
      // }
      // return data
      // generate all month of year
      // const data = []
      // for(let i = 0; i < 12; i++) {
      //   const val = randomInteger(70, 160)
      //   const minute = randomInteger(0, 59)
      //   const hour = randomInteger(0, 23)
      //   const day = randomInteger(1, 28)

      //   data.push({
      //     date: new Date(2020, i, day, hour, minute),
      //     value: val
      //   })
      // }
      // return data
      // generate years diapason
      // const data = []
      // for(let i = 2000; i < 2014; i++) {
      //   const val = randomInteger(70, 160)
      //   const minute = randomInteger(0, 59)
      //   const hour = randomInteger(0, 23)
      //   const day = randomInteger(1, 28)
      //   const month = randomInteger(1, 11)

      //   data.push({
      //     date: new Date(i, month, day, hour, minute),
      //     value: val
      //   })
      // }
      // console.log(data);
      // return data
    },
    parseBarData(arr) {
      return this.barPoints.map(item => {
        return {
          date: this.$moment(item.date?.from).toDate(),
          value: item.average,
        }
      });
    },
    newRender () {

      // Themes begin
      // am4core.useTheme(am4themes_animated);
      // Themes end

      // Create chart instance
      var chart = am4core.create(this.$refs.chartdiv, am4charts.XYChart);
      chart.hiddenState.properties.opacity = 0; // this creates initial fade-in
      // init chart locale 
      chart.language.locale = am4lang_ru_RU;
      // chart.data = this.parseData()

      chart.data = this.parseBarData()

      // Set input format for the dates
      // chart.dateFormatter.inputDateFormat = "yyyy-MM-dd";

      // Create axes
      var dateAxis = chart.xAxes.push(new am4charts.DateAxis());
      var valueAxis = chart.yAxes.push(new am4charts.ValueAxis());

      // valueAxis.rangeChangeDuration = 5000;

      valueAxis.tooltip.disabled = true;
      // valueAxis.extraMax = 0.05
      // valueAxis.extraMin = 0.05
      valueAxis.start = 0
      valueAxis.min = 0;
      // valueAxis.renderer.gridContainer.zIndex = 1;
      valueAxis.cursorTooltipEnabled = false;

      // valueAxis.renderer.labels.template.dy = -20;

      var gridIntervals = [
        { timeUnit: "millisecond", count: 1 },
        { timeUnit: "millisecond", count: 5 },
        { timeUnit: "millisecond", count: 10 },
        { timeUnit: "millisecond", count: 50 },
        { timeUnit: "millisecond", count: 100 },
        { timeUnit: "millisecond", count: 500 },
        { timeUnit: "second", count: 1 },
        { timeUnit: "second", count: 5 },
        { timeUnit: "second", count: 10 },
        { timeUnit: "second", count: 30 },
        { timeUnit: "minute", count: 1 },
        { timeUnit: "minute", count: 5 },
        { timeUnit: "minute", count: 10 },
        { timeUnit: "minute", count: 30 },
        { timeUnit: "hour", count: 1 },
        { timeUnit: "hour", count: 2 },
        { timeUnit: "hour", count: 3 },
        { timeUnit: "hour", count: 6 },
        { timeUnit: "hour", count: 12 },
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
      // chart.paddingRight = 20
      // chart.paddingLeft = 20

      // при минутном
      // dateAxis.dateFormats.setKey("minute", "HH:mm");
      // dateAxis.periodChangeDateFormats.setKey("minute", "HH:mm");
      // dateAxis.baseInterval = {
      //   timeUnit: "minute",
      //   count: 5
      // };
      // dateAxis.renderer.minGridDistance = 40;
      // dateAxis.renderer.grid.template.location = 0;

      // dateAxis.start = 0.98

      dateAxis.dateFormats.setKey("hour", "HH:mm");
      dateAxis.periodChangeDateFormats.setKey("hour", "HH:mm");
      dateAxis.baseInterval = {
        timeUnit: "hour",
        count: 1
      };
      dateAxis.renderer.minGridDistance = 45;
      
      // dateAxis.renderer.grid.template.location = 0.5;
      dateAxis.start = 0.99;
      // dateAxis.renderer.labels.template.location = 0.5;
      dateAxis.groupData = true;
      // dateAxis.skipEmptyPeriods = true;
      // dateAxis.groupCount = 50;
      dateAxis.keepSelection = true;
      dateAxis.groupInterval = { timeUnit: "minute", count: 1 };

      dateAxis.events.on('rangechangeended', (ev) => {
        // setTimeout(() => {

          let axis = ev.target;
          let start = axis.getPositionLabel(axis.start);
          let end = axis.getPositionLabel(axis.end);

          // let start1 = new Date(ev.target.minZoomed);
          // let end1 = new Date(ev.target.maxZoomed);
          // console.log("New range: " + start + " -- " + end);
          // console.log("New range: " + start1 + " -- " + end1);
          //последняя точка
          const lastPoint = this.$moment(this.point)
          //дата начала/конца базовой видимой шалы
          const startDate = this.$moment(ev.target.minZoomed)
          const endDate = this.$moment(ev.target.maxZoomed)
          //находим середину между датой начала и конца видимой шкалы
          const diffStartEndMinutes = this.$moment.duration(endDate.diff(startDate)).asMinutes() / 2
          const centerDate = endDate.clone().subtract(diffStartEndMinutes, 'minutes')
          const startOfCenterDate = centerDate.startOf('day')

          const lastDateNew = lastPoint.clone().startOf('day')

          //  dateAxis.start = 0.98;

          // if (!lastDateNew.isSame(startOfCenterDate)) {
            this.point = startOfCenterDate;
            // this.$store.commit('monitoringv2/SET_LEGEND')
          // }

        // }, 300)
      })

      // var range = valueAxis.axisRanges.create();
      // range.value = 85;
      // range.grid.stroke = am4core.color("#396478");
      // range.grid.strokeWidth = 2;
      // range.grid.strokeOpacity = 1;

      var clickedBullets = []

      const createSeries = (valueProp, color) => {
        // Create series
        var series = chart.series.push(new am4charts.LineSeries());
        series.dataFields.valueY = valueProp;
        series.dataFields.dateX = "date";
        // series.tooltipText = "{value}"
        series.stroke = color;
        series.strokeWidth = 2;
        series.minBulletDistance = 15;
        series.showOnInit = false;
        series.hiddenState.transitionDuration = 1000;
        series.hiddenState.transitionEasing = am4core.ease.elasticInOut;
        // series.dataFields.openValueY = "open";
        // series.dataFields.closeValueY = "close";

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
            element.strokeOpacity = 1

            element.bulletsContainer.children.values.forEach(bullet => {
              bullet.children.values[0].fill = am4core.color("#fff")
              bullet.children.values[0].scale = 1
            })
          });
          
          
          // reset styles circles
          // clickedBullets.forEach(item => {
          //   item.fill = am4core.color("#fff")
          //   item.scale = 1
          // })
          // clickedBullets = []
          // init circles styles
          chart.series.values.forEach(element => {
            element.strokeOpacity = 0.3
            const bulElement = element.bulletsContainer.children.values?.[ev.target.dataItem.index]
            if (!bulElement) {
              return
            }
            bulElement.children.values[0].fill = element.stroke;
            bulElement.children.values[0].scale = 1.3
            // clickedBullets.push(bulElement.children.values[0])
          });
          // ev.target.circle.radius = 2;

          // clickedBullets.push(ev.target.circle)
        })

        return series
      }

      const seriesArray = []

      // seriesArray.push(createSeries('value', '#000'))
      // seriesArray.push(createSeries('systolic', 'red'))
      // seriesArray.push(createSeries('diastolic', 'green'))
      chart.maskBullets = false;

      const createBarSeries = () => {
        var series = chart.series.push(new am4charts.ColumnSeries());
        series.dataFields.valueY = "value";
        series.dataFields.dateX = "date";
        series.minBulletDistance = 15;
        // series.tooltipText = "{value}";
        // series.tooltip.pointerOrientation = "vertical";
        // series.tooltip.disabled = true
        // series.tooltip.hiddenState.properties.opacity = 1;
        // series.tooltip.hiddenState.properties.visible = false;
        // series.stacked = true
        // series.minBulletDistance = 0;

        // series.tooltip.adapter.add("disabled", function(disabled, target) {
        //   // alternatively use this:
        //   // if (target.dataItem && target.dataItem.dataContext && target.dataItem.dataContext.visits === 0) {
        //   return true
        // });

        var bullet = series.bullets.push(new am4charts.Bullet());

        var triangle = bullet.createChild(am4core.Triangle);
        triangle.width = 15;
        triangle.height = 13;
        triangle.dy = -3;
        triangle.direction = "bottom";
        // triangle.propertyFields.fill = "color";
        // triangle.propertyFields.fillOpacity = "opacity";
        triangle.fillOpacity = 0;
        triangle.fill = am4core.color("#0096C8");
        triangle.stroke = am4core.color("#0096C8");
        // triangle.disabled = true
        triangle.strokeWidth = 0;
        triangle.horizontalCenter = "middle";
        triangle.verticalCenter = "bottom";


        var columnTemplate = series.columns.template;
        columnTemplate.strokeWidth = 0;
        columnTemplate.strokeOpacity = 0;
        columnTemplate.width = 10;
        columnTemplate.fill = am4core.color("#0096C8");

        // Add slice click event
        // var currentSlice;
        series.columns.template.events.on("hit", function(ev) {
          ev.event.stopPropagation()
          // triangle.disabled = false
          ev.target.dataItem.bullets.each((id, bullet) => {
            // bullet.fillOpacity = 1
            bullet.children.values[0].fillOpacity = 1
          })
          // console.log(ev.target.bullets.values[0].children.values[0].fillOpacity);
          // if (currentSlice) {
          //   currentSlice.tooltip.hide();
          // }
          // currentSlice = ev.target;
          // currentSlice.tooltipText = "{value}";
          // currentSlice.showTooltip();
        }, this);

        // columnTemplate.events.on('hit', (ev) => {
        //   dateAxis.showTooltipAtPosition(0.5);
        //   console.log('click');
        // })

        // var bullet = series.bullets.push(new am4charts.LabelBullet())
        // bullet.interactionsEnabled = false
        // bullet.dy = 30;
        // bullet.label.text = '{value}'
        // bullet.label.fill = am4core.color('#ffffff')
      }

      createBarSeries()

      chart.events.on('hit', (ev) => {
        console.log('chartClick');
        if (ev.event.cancelBubble) {
          return
        }

        chart.series.values.forEach(element => {
          element.strokeOpacity = 1

          element.bulletsContainer.children.values.forEach(bullet => {
            bullet.children.values[0].fillOpacity = 0
            // bullet.children.values[0].scale = 1
          })
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
          indicator.width = am4core.percent(50);
          indicator.height = am4core.percent(50);
          indicator.align = "center";
          indicator.valign = "middle";
          // indicator.x = am4core.percent(50);
          // indicator.y = am4core.percent(50);

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

        // chart.data.sort(function(a, b) {
        //   return (a.date) - (b.date);
        // })
        
      });

      // chart.events.on('beforedatavalidated', (ev) => {
      //   if (chart.isReady()) {
      //     console.log('data validated');
      //     ev.target.data.unshift({
      //       date: this.$moment('2021-03-23T14:25:00').toDate(),
      //       value: 80,
      //       systolic: 67,
      //       diastolic: 77,
      //       open: 70,
      //       close: 120
      //     })
      //   }
      // })

      // setTimeout(() => {
      //   chart.invalidateData()
      // }, 5000)


      chart.events.on('ready', (ev) => {
        // chart.series.values.forEach((element, groupIndex) => {
          // const bulElement = element.bulletsContainer.children.values?.[groupIndex]

          // element.bulletsContainer.children.values.forEach(bullet => {
          //   this.allBulets.push({
          //     ind: groupIndex,
          //     bul: bullet
          //   })
          // })
          // bulElement.children.values[0].fill = element.stroke;
          // bulElement.children.values[0].scale = 1.3
          // clickedBullets.push(bulElement.children.values[0])
        // });
        // dateAxis.start = 0.98;
        // dateAxis.zoomToDates(
        //   new Date(2021, 2, 23),
        //   new Date(2021, 2, 23)
        // );
        // dateAxis.start = 0.99;
        // dateAxis.end = 0.5;
        dateAxis.tooltip.disabled = true;
        // dateAxis.start = 1;
        // dateAxis.startLocation = 0.49;
        // dateAxis.endLocation = 0.51;
        // dateAxis.skipEmptyPeriods = true;

        // dateAxis.zoomToDates(
        //   new Date(2021, 2, 23, 14, 32),
        //   new Date(2021, 2, 23, 14, 51)
        // );
        // dateAxis.start = 0.98;
      })

      // ----------------
      // bullethover.properties.cursorOptions = am4core.MouseCursorStyle.grabbing;
      // Make a panning cursor
      const cursor = new am4charts.XYCursor();
      cursor.behavior = "panX";
      chart.cursor = cursor
      // chart.cursor.xAxis = dateAxis;
      // chart.cursor.snapToSeries = seriesArray;
      cursor.lineX.disabled = true;
      chart.cursor.opacity = 0;
      chart.cursor.lineY.opacity = 0;
      chart.cursor.lineX.opacity = 0;
      chart.scrollbarX = new am4core.Scrollbar();
      chart.scrollbarX.animationDuration = 5000;
      // chart.scrollbarX.events.on('wheel', (ev) => {
      //   console.log(ev);
      // })
      chart.scrollbarX.disabled = true;
      chart.zoomOutButton.disabled = true;
      chart.swipeable = true;

      // const newData = {
      //   close: 160,
      //   date: this.$moment('2021-03-22T10:46:00').toDate(),
      //   diastolic: 165,
      //   open: 70,
      //   systolic: 180,
      //   value: 103
      // }

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
  width: 832px;
  height: 300px;
}

.main {
  display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}
</style>
