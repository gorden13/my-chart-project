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
    <!-- <input type="time"> -->
    <!-- <div v-for="(input, index) of inputs" :key="index">
      <label>{{ input.value }}</label>
      <input type="text" v-model="inputs[index].value">
    </div>
    <button @click="save"></button> -->

    <!-- <input type="number"> -->

    <button type="button" @click="scrollTo">scroll to</button>

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
    inputs: [
      {
        label: 'input 1',
        value: null
      },
      {
        label: 'input 2',
        value: null
      },
      {
        label: 'input 2',
        value: null
      }
    ],
    points: result?.['test2'] || [],
    pointsNew: result?.['test'] || [],
    barPoints: result?.['sleep3'] || [],
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
    scrollTo() {
      this.dateAxis.zoomToDates(
        new Date(2021, 3, 29),
        new Date(2021, 3, 30),
        false,
        true // this makes zoom instant
      );
    },
    save () {
      console.log(this.inputs);
    },
    validateNumber(evt) {
      let theEvent = evt || window.event;
      let key = theEvent.keyCode || theEvent.which;
      key = String.fromCharCode(key);
      var regex = /[0-9]|\./;
      if (!regex.test(key)) {
        theEvent.returnValue = false;
        if (theEvent.preventDefault) theEvent.preventDefault();
        return false
      }
    },
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
      categoryAxis.start = 0.99;
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
    getSleepPeriods(arr) {
      let res = {
        from: null,
        to: null
      }

      if (arr.length) {
        res.from = this.$moment(arr[0].from).toDate();
        if (arr.length > 1) {
          res.to = this.$moment(arr[arr.length - 1].to).toDate();
        } else {
          res.to = this.$moment(arr[0].to).toDate();
        }
      }
      return res
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
    parseSleep(arr) {
      // this.barPoints.sort(
      //   (a, b) => this.$moment(a.date.from).valueOf() - this.$moment(b.date.from).valueOf()
      // )
      // return this.barPoints.map(item => {
      //   return {
      //     date: this.$moment(item.date?.from).toDate(),
      //     sleep: item.sleep.yAxis,
      //     inBed: item.inBed.yAxis,
      //     wake: item.wake.yAxis,
      //     main: 100,
      //     inBedArray: this.getSleepPeriods(item.inBed.periods),
      //     inSleepArray: this.getSleepPeriods(item.sleep.periods),
      //     inWakeArray: this.getSleepPeriods(item.wake.periods)
      //   }
      // });

      const putsPeriods = (periodsArray, propName, color) => {
        const newArray = periodsArray.map(item => {
          const tempObj = { 
            category: '',
            from: this.$moment(item.from).toDate(),
            to: this.$moment(item.to).toDate(),
            color: am4core.color(color)
          }

          tempObj[propName] = 100

          return tempObj
        })

        return newArray
      }


      let array = []

      this.barPoints.forEach(item => {
        // array.push({
        //   date: this.$moment(item.date?.from).toDate(),
        //   sleep: item.sleep.yAxis,
        //   inBed: item.inBed.yAxis,
        //   wake: item.wake.yAxis,
        //   main: 100,
        //   inBedArray: this.getSleepPeriods(item.inBed.periods),
        //   inSleepArray: this.getSleepPeriods(item.sleep.periods),
        //   inWakeArray: this.getSleepPeriods(item.wake.periods)
        // })

        // array.push({
        //   date: this.$moment(item.date?.to).toDate(),
        //   sleep: null,
        //   inBed: null,
        //   wake: null,
        //   main: 100,
        //   inBedArray: {},
        //   inSleepArray: {},
        //   inWakeArray: {}
        // })
        // array = [...array, {
        //   category: '',
        //   from: this.$moment(item.date.from).toDate(),
        //   to: this.$moment(item.date.to).toDate(),
        //   color: am4core.color('#fff')
        // }]
        // складываем все точки пребывания в постели, итд.
        array = [...array, ...putsPeriods(item.inBed.periods, 'inBed', '#0096C8')]
        array = [...array, ...putsPeriods(item.sleep.periods, 'sleep', '#77BCD4')]
        array = [...array, ...putsPeriods(item.wake.periods, 'wake', '#BBDFEB')]

        // console.log(array);

      })

      return array.sort(
        (a, b) => this.$moment(a.from).valueOf() - this.$moment(b.from).valueOf()
      )
    },
    newRender () {

      // Themes begin
      // am4core.useTheme(am4themes_animated);
      // Themes end

      // Create chart instance
      var chart = am4core.create(this.$refs.chartdiv, am4charts.XYChart);
      this.chart = chart
      chart.hiddenState.properties.opacity = 0; // this creates initial fade-in
      // init chart locale 
      chart.language.locale = am4lang_ru_RU;
      // chart.data = this.parseData()

      // console.log(this.parseSleep());

      const dataPoints = this.parseSleep()

      console.log(dataPoints);

      chart.data = dataPoints

      // chart.seriesContainer.zIndex = -1;

      // console.log(chart.data);

      // Set input format for the dates
      // chart.dateFormatter.inputDateFormat = "yyyy-MM-dd";

      // Create axes
      var dateAxis = chart.xAxes.push(new am4charts.DateAxis());
      this.dateAxis = dateAxis
      var valueAxis = chart.yAxes.push(new am4charts.CategoryAxis());
      

      // valueAxis.rangeChangeDuration = 5000;
      // valueAxis.includeRangesInMinMax = true

      valueAxis.tooltip.disabled = true;
      // valueAxis.valign = "right";
      // valueAxis.extraMax = 0.05
      // valueAxis.extraMin = 0.05
      // valueAxis.start = 0

      // for sleep 1 day scale
      // valueAxis.max = 200;
      // valueAxis.min = 0;
      valueAxis.renderer.labels.template.stroke = am4core.color('#98B6C8');
      valueAxis.renderer.grid.template.disabled = true;
      // valueAxis.renderer.labels.template.disabled = true;
      valueAxis.dataFields.category = "category";
      dateAxis.renderer.grid.template.strokeDasharray = '4.3'
      dateAxis.renderer.grid.template.strokeOpacity = 1
      dateAxis.groupData = false;
      dateAxis.renderer.labels.template.location = 0.001;
      dateAxis.startLocation = 0.3;
      dateAxis.endLocation = -0.3;
      //

      // valueAxis.strictMinMax = true;
      // valueAxis.calculateTotals = true;
      // valueAxis.renderer.minWidth = 150;
      // valueAxis.renderer.gridContainer.zIndex = 1;
      // valueAxis.cursorTooltipEnabled = false;

      // const range = valueAxis.axisRanges.create()
      // range.value = 10000
      // // range.grid.above = true;
      // range.grid.stroke = am4core.color("#F66D6D")
      // range.grid.strokeWidth = 2
      // range.grid.strokeOpacity = 1

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
      // dateAxis.gridIntervals.setAll(gridIntervals);
      // chart.paddingRight = 20
      // chart.paddingLeft = 20

      // при минутном
      dateAxis.dateFormats.setKey("hour", "HH:mm");
      dateAxis.periodChangeDateFormats.setKey("hour", "HH:mm");
      dateAxis.baseInterval = {
        timeUnit: "minute",
        count: 1
      };
      dateAxis.renderer.minGridDistance = 50;
      // dateAxis.renderer.grid.template.location = 0;

      // dateAxis.start = 0.98

      // dateAxis.dateFormats.setKey("day", "dd");
      // dateAxis.periodChangeDateFormats.setKey("day", "dd");
      // dateAxis.baseInterval = {
      //   timeUnit: "day",
      //   count: 1
      // };
      // dateAxis.renderer.minGridDistance = 75;
      
      
      dateAxis.start = 0.99;
      // dateAxis.renderer.labels.template.location = 0.5;
      // dateAxis.groupData = true;
      // dateAxis.skipEmptyPeriods = true;
      // dateAxis.groupCount = 50;
      dateAxis.keepSelection = true;
      
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

      let columnsArray = []

      const createBarSeries = (valueY, color, opacity) => {
        var series = chart.series.push(new am4charts.ColumnSeries());
        series.dataFields.valueY = valueY;
        series.dataFields.dateX = "date";
        // series.stacked = true;
        // series.noRisers = true;
        // series.dataItems.template.locations.categoryX = 0.5;

        var bullet = series.bullets.push(new am4charts.Bullet());

        var triangle = bullet.createChild(am4core.Triangle);
        triangle.width = 15;
        triangle.height = 13;

        triangle.dy = -5;
        triangle.direction = "bottom";
        triangle.visible = false
        triangle.fill = am4core.color(color);
        triangle.stroke = am4core.color(color);
        triangle.strokeWidth = 0;
        triangle.horizontalCenter = "middle";
        triangle.verticalCenter = "bottom";

        var columnTemplate = series.columns.template;
        columnTemplate.strokeWidth = 1;
        columnTemplate.strokeOpacity = 0;
        // columnTemplate.fillOpacity = 0;
        columnTemplate.stroke = am4core.color("#fff");
        columnTemplate.height = am4core.percent(90);
        columnTemplate.cursorOverStyle = am4core.MouseCursorStyle.pointer;

        // // при минутном
        // columnTemplate.width = 10;
        // // при часовом
        columnTemplate.width = am4core.percent(100);

        columnTemplate.fill = am4core.color(color);

        series.columns.template.events.on("inited", (ev) => {
          if (this.selectedColumn) {
            ev.target.fillOpacity = 0.5
          }
          columnsArray.push({
            column: ev.target,
            groupIndex: ev.target.dataItem.index
          })
        })

        series.columns.template.events.on("hit", function(ev) {
          ev.event.stopPropagation()
          chart.series.values.forEach(element => {
            element.bulletsContainer.children.values.forEach(bullet => {
              bullet.children.values[0].visible = false
            })
          });
          
          this.point = ev.target.dataItem.dataContext.date
          this.pointInfo = ev.target.dataItem.dataContext
          this.selectedColumn = ev.target.dataItem.dataContext
          ev.target.dataItem.bullets.each((id, bullet) => {
            bullet.children.values[0].visible = true
          })

          
          columnsArray.forEach(item => {
            console.log(item);  
            if (item.groupIndex !== ev.target.dataItem.index) {
              item.column.fillOpacity = 0.5
            } else {
              item.column.fillOpacity = 1
            }

            // item.fillOpacity = 0.5

          })
        }, this);

        return series
      }

      // createRange(0, 20, am4core.color("#19d228"));
      // createRange(20, 40, am4core.color("#b4dd1e"));
      // createRange(40, 60, am4core.color("#f4fb16"));

      // createBarSeries('sleep', "#0096C8", 1)
      // createBarSeries('inBed', "#77BCD4", 1)
      // createBarSeries('wake', "#BBDFEB", 1)
      // const series = createBarSeries('main', "#BBDFEB", 1)

      const createSeriesForSleep = () => {
        const series = chart.series.push(new am4charts.LineSeries());
        series.dataFields.valueY = "value";
        series.dataFields.dateX = "date";
        series.stroke = am4core.color("#96BBBB");
        series.strokeWidth = 1;
        series.strokeOpacity = 0;
        series.fill = am4core.color("#96BBBB");
        series.fillOpacity = 1;
        series.tensionX = 0.5;

        series.propertyFields.fill = "color";
        series.propertyFields.stroke = "color";
      }

      const createBarSeriesForSleep = (color) => {
        var series = chart.series.push(new am4charts.ColumnSeries());
        // series.stacked = true;
        // series.noRisers = true;
        // series.dataItems.template.locations.categoryX = 0.5;
        series.dataFields.dateX = "to";
        series.dataFields.openDateX = "from";
        series.dataFields.categoryY = "category";
        series.columns.template.propertyFields.fill = "color";
        series.columns.template.strokeOpacity = 0;
        series.columns.template.paddingTop = 60
        series.columns.template.height = am4core.percent(50);
        series.columns.template.cursorOverStyle = am4core.MouseCursorStyle.pointer;
        // series.columns.template.height = am4core.percent(100);

        // var bullet = series.bullets.push(new am4charts.Bullet());

        // var triangle = bullet.createChild(am4core.Triangle);
        // triangle.width = 15;
        // triangle.height = 13;

        // triangle.dy = 40;
        // // triangle.dx = 0
        // triangle.direction = "bottom";
        // triangle.visible = false
        // triangle.fill = am4core.color(color);
        // triangle.stroke = am4core.color(color);
        // triangle.strokeWidth = 0;
        // triangle.horizontalCenter = "middle";
        // triangle.verticalCenter = "bottom";

        // series.columns.template.tooltipY = 0;
        // series.columns.template.tooltipText = ' '
        // series.tooltip.pointerOrientation = "down";
        // series.columns.template.showTooltipOn = "hit";
        // series.tooltip.background.cornerRadius = 5;
        // console.log(series.tooltip.background);
        // series.tooltip.background.strokeOpacity = 0;
        // series.tooltip.pointerOrientation = "vertical";
        // series.tooltip.label.minWidth = 20;
        // series.tooltip.label.minHeight = 20;
        // series.tooltip.label.textAlign = "middle";
        // series.tooltip.label.textValign = "middle";

        series.columns.template.events.on("hit", function(ev) {
          ev.event.stopPropagation()
          chart.series.values.forEach(element => {
            element.bulletsContainer.children.values.forEach(bullet => {
              bullet.children.values[0].visible = false
            })
          });
          
          this.point = ev.target.dataItem.dataContext.from
          this.pointInfo = ev.target.dataItem.dataContext
          this.selectedColumn = ev.target.dataItem.dataContext
          ev.target.dataItem.bullets.each((id, bullet) => {
            bullet.children.values[0].visible = true
          })

        }, this)
      }

      // createSeriesForSleep()

      createBarSeriesForSleep("#0096C8")

       /* Create ranges */
      function createRange(from, to, color) {
        // var range = axis.axisRanges.create();
        // range.value = from;
        // range.endValue = to;
        // range.axisFill.fill = color;
        // range.axisFill.fillOpacity = 0.8;
        // range.label.disabled = true;

        var range = dateAxis.axisRanges.create();
        range.date = from;
        range.endDate = to;
        range.axisFill.fillOpacity = 1;
        range.axisFill.stroke = am4core.color(color);
        range.axisFill.fill = am4core.color(color);
        range.axisFill.above = true;
        // range.axisFill.fillOpacity = 1
        range.axisFill.strokeOpacity = 0
        range.label.disabled = true;
        range.axisFill.stroke = am4core.color('#fff');
        console.log(range);

        // range.contents.fillOpacity = 0.5;
      }

      var length = dataPoints.length

      // dataPoints.forEach(item => {
      //   if (item.inBedArray.from && item.inBedArray.to) {
      //     // console.log(`create range from: ${item.inBedArray.from} , to: ${item.inBedArray.to}`);
      //     createRange(item.inBedArray.from, item.inBedArray.to, "#19d228")
      //   }
      //   if (item.inSleepArray.from && item.inSleepArray.to) {
      //     // console.log('sleep');
      //     createRange(item.inSleepArray.from, item.inSleepArray.to, "#b4dd1e")
      //   }
      //   if (item.inWakeArray.from && item.inWakeArray.to) {
      //     createRange(item.inWakeArray.from, item.inWakeArray.to, "#fb7116")
      //   }
      // })

      // createRange(dataPoints[0].inBedArray.from, dataPoints[0].inBedArray.to)

      chart.events.on('hit', (ev) => {
        console.log('chartClick');
        if (ev.event.cancelBubble) {
          return
        }

        this.selectedColumn = null
        this.pointInfo = null

        columnsArray.forEach(item => {
          item.column.fillOpacity = 1
        })

        chart.series.values.forEach(element => {
          // element.fillOpacity = 1

          element.bulletsContainer.children.values.forEach(bullet => {
            bullet.children.values[0].visible = false
            // bullet.children.values[0].scale = 1
          })
        });
      })

      chart.events.on("beforevalidated", (ev) => {
        // check if there's data
        // if (ev.target.data.length == 0) {
        //   showIndicator();
        // }
        // else if (indicator) {
        //   hideIndicator();
        // } else {
          this.point = ev.target.data?.[ev.target.data.length - 1]?.date
        // }

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
      // chart.scrollbarY = new am4core.Scrollbar();
      chart.scrollbarX.animationDuration = 5000;

      // chart.cursor.events.on("cursorpositionchanged", function(ev) {
      //     var yAxis = ev.target.chart.yAxes.getIndex(0);
      //     console.log("y: ", yAxis.positionToValue(yAxis.toAxisPosition(ev.target.yPosition)));
      // });

      // const topContainer = chart.chartContainer.createChild(am4core.Container)
      // topContainer.layout = "absolute"
      // topContainer.toBack()
      // topContainer.paddingBottom = 10
      // topContainer.width = am4core.percent(100)
      // topContainer.align = "center"

      // const dateLabel = topContainer.createChild(am4core.Label)
      // dateLabel.text = '19.05.2021'
      // dateLabel.align = "center"
      // dateLabel.fill = am4core.color("#000")
      // dateLabel.fontFamily = "Foros"
      // // dateLabel.width = am4core.percent(50)

      // const label = chart.createChild(am4core.Label);
      // label.text = '[bold] 45 6034 шагов [/]'
      // label.fontSize = 14;
      // label.align = "center";
      // label.isMeasured = false;
      // label.x = am4core.percent(50);
      // label.y = 20;
      // label.padding(0, 5, 0, 5);
      // label.background.fill = am4core.color("red");
      // label.horizontalCenter = "middle";
      // label.contentAlign = "center"
      // label.fontFamily = "Foros"
      // this.dateLabel = '19.05.2021 45 6034 шагов'
      // chart.scrollbarY.start = 0;
      // chart.scrollbarY.end = 0.08;
      // chart.scrollbarX.events.on('wheel', (ev) => {
      //   console.log(ev);
      // })
      chart.scrollbarX.disabled = true;
      chart.zoomOutButton.disabled = true;
      chart.swipeable = true;

      let info = chart.plotContainer.createChild(am4core.Container);
      info.width = am4core.percent(40);
      info.height = 50;
      // info.x = am4core.percent(50);
      info.y = -2;
      info.align = "center"
      info.paddingTop = 7
      // info.padding(5, 10, 10, 5);
      // info.paddingRight = 7
      info.background.fill = am4core.color("red");
      info.background.fillOpacity = 1;
      info.layout = "absolute";

      let titleLabel = info.createChild(am4core.Label);
      titleLabel.text = "17 апреля";
      titleLabel.align = "center"
      titleLabel.marginTop = 15
      // titleLabel.minWidth = 60;

      let titleLabel1 = info.createChild(am4core.Label);
      titleLabel1.text = "20 000 шагов";
      titleLabel1.align = "center"
      titleLabel1.y = 25
      titleLabel1.visible = false

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

input[type="date"]::-webkit-calendar-picker-indicator {
  display: none;
}
</style>
