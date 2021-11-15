<template>
  <div class="main">
    <div class="hello" ref="chartdiv">
    </div>
    <code style="margin-top: 25px; font-size: 20px;">
      {{ pointInfo }}
    </code>
  </div>
</template>

<script>
import * as am4core from "@amcharts/amcharts4/core";
import * as am4charts from "@amcharts/amcharts4/charts";
import am4themes_animated from "@amcharts/amcharts4/themes/animated";
import am4lang_ru_RU from "@amcharts/amcharts4/lang/ru_RU";

am4core.useTheme(am4themes_animated);
am4core.options.autoDispose = true;

import result from '/data.json'

export default {
  name: 'HelloWorld',
  data: () => ({
    points: result?.['test2'] || [],
    pointsNew: result?.['test'] || [],
    barPoints: result?.['pulsePoints5min'] || [],
    point: null,
    pointInfo: null,
    clickedBullets: [],
    lastDate: null,
    allBulets: [],
    selectedColumn: null,
    inBedArray: [],
    chart: null,
    dateAxis: null
  }),
  mounted() {
    this.newRender();
  },
  methods: {
    parseBarData(arr) {
      return this.barPoints.filter(item => item.isRealPoint).map(item => {
        return {
          date: this.$moment(item.date).toDate(),
          value: item.parameter,
        }
      });
    },
    newRender () {
      this.chart = am4core.create(this.$refs.chartdiv, am4charts.XYChart);
      const chart = this.chart;
      chart.language.locale = am4lang_ru_RU;
      chart.hiddenState.properties.opacity = 0;
      
      // chart.dateFormatter.inputDateFormat = "yyyyMMdd  HH:mm:ss"
      const dataPoints = this.parseBarData()
      console.log(dataPoints);

      chart.data = dataPoints
      // для эффекта скролла
      chart.cursor = new am4charts.XYCursor();
      chart.cursor.behavior = "panX";
      // chart.cursor.lineY.opacity = 0;
      // chart.cursor.lineX.opacity = 0;
      chart.cursor.lineY.disabled = true;
      chart.cursor.lineX.disabled = true;

      chart.zoomOutButton.disabled = true;
      // chart.preloader.disabled = true;
      chart.numberFormatter.numberFormat = "#.0a";
      chart.swipeable = true;

      chart.bottomAxesContainer.layout = "absolute";

      // Create axes
      var dateAxis = chart.xAxes.push(new am4charts.DateAxis());
      var valueAxis = chart.yAxes.push(new am4charts.ValueAxis());
      
      valueAxis.tooltip.disabled = true;
      // valueAxis.valign = "right";
      // valueAxis.extraMax = 0.05
      // valueAxis.extraMin = 0.05
      // valueAxis.start = 0

      // dateAxis.renderer.labels.template.location = 0.001;
      // dateAxis.groupInterval = { timeUnit: "minute", count: 5 }

      // valueAxis.strictMinMax = true;
      // valueAxis.calculateTotals = true;
      // valueAxis.renderer.minWidth = 150;
      // valueAxis.renderer.gridContainer.zIndex = 1;
      // valueAxis.cursorTooltipEnabled = false;

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
      // dateAxis.gridIntervals.setAll(gridIntervals);

      // при минутном
      // dateAxis.dateFormats.setKey("hour", "HH:mm");
      // dateAxis.periodChangeDateFormats.setKey("hour", "HH:mm");
      
      // dateAxis.renderer.minGridDistance = 75;
      // dateAxis.renderer.grid.template.location = 0.001;

      // dateAxis.start = 0.98

      dateAxis.dateFormats.setKey("hour", "HH:mm");
      dateAxis.keepSelection = true;
      dateAxis.periodChangeDateFormats.setKey("hour", "HH:mm");
      dateAxis.renderer.minGridDistance = 75;
      
      dateAxis.baseInterval = {
        timeUnit: "minute",
        count: 5
      };
      dateAxis.start = 0.93
      dateAxis.groupData = true;
      dateAxis.groupInterval = {
        timeUnit: "minute",
        count: 5
      }

      dateAxis.renderer.labels.template.location = 0.001;

      // dateAxis.startLocation = 0.1;
      // dateAxis.endLocation = 0.9;

      // dateAxis.renderer.labels.template.location = 0.5;
      // dateAxis.groupData = true;
      // dateAxis.skipEmptyPeriods = true;
      // dateAxis.groupCount = 50;
      // dateAxis.keepSelection = true;

      this.dateAxis = dateAxis
      
      // при минутном
      // dateAxis.groupInterval = { timeUnit: "minute", count: 1 };
      // при часовом
      

      // dateAxis.groupInterval = { timeUnit: "hour", count: 1 };
      // dateAxis.renderer.labels.template.location = 0.00001;
      // dateAxis.renderer.grid.template.location = 0;
      // dateAxis.startLocation = -0.3;
      // dateAxis.endLocation = 1.1;
      // dateAxis.end = 1.3;

      // dateAxis.renderer.grid.template.location = 0;
      
      // dateAxis.renderer.minLabelPosition = 0.05;
      // dateAxis.renderer.maxLabelPosition = 0.95;
      // dateAxis.renderer.cellStartLocation = 0.5;
      // dateAxis.renderer.cellEndLocation = 0.99;
      // dateAxis.startLocation = -0.3;
      
      // dateAxis.endLocation = 0.5

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


      var columnsArray = []
      const createBarSeries = (valueY, color, opacity) => {

        const series = chart.series.push(new am4charts.ColumnSeries());
        series.dataFields.valueY = valueY;
        series.dataFields.dateX = "date";
        series.name = 'sds';
        series.stacked = true;
        series.clustered = false;

        // series.stacked = true;
        // series.noRisers = true;
        // series.dataItems.template.locations.categoryX = 0.5;

        const columnTemplate = series.columns.template;
        columnTemplate.strokeWidth = 1;
        columnTemplate.strokeOpacity = 1;
        columnTemplate.stroke = am4core.color("#fff");
        columnTemplate.fill = am4core.color(color);
        columnTemplate.cursorOverStyle = am4core.MouseCursorStyle.pointer;
        columnTemplate.width = am4core.percent(90);

        // columnTemplate.fill = am4core.color(color);

        // series.columns.template.events.on("inited", (ev) => {
        //   if (this.selectedColumn) {
        //     ev.target.fillOpacity = 0.5
        //   }
        //   columnsArray.push({
        //     column: ev.target,
        //     groupIndex: ev.target.dataItem.index
        //   })
        // })

        series.columns.template.events.on("hit", (e) => {
          e.event.stopPropagation();
          this.pointInfo = e.target.dataItem.dataContext
        });
      }

      createBarSeries('value', '#0096c8')

      chart.events.on('hit', (ev) => {
        if (ev.event.cancelBubble) {
          return
        }

        this.selectedColumn = null
        this.pointInfo = null

        columnsArray.forEach(item => {
          item.column.fillOpacity = 1
        })

        chart.series.values.forEach(element => {

          element.bulletsContainer.children.values.forEach(bullet => {
            bullet.children.values[0].visible = false
          })
        });
      })

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

input[type="date"]::-webkit-calendar-picker-indicator {
  display: none;
}
</style>
