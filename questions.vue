<script>
// LINE 55 is where I am having an issue.
import { get, call } from 'vuex-pathify'
import * as am4core from '@amcharts/amcharts4/core'
// eslint-disable-next-line no-unused-vars
import * as am4charts from '@amcharts/amcharts4/charts'
am4core.addLicense('')

// globaFilter is the data that will change based on a dropdown selection.
export default {
  props: {
    widget: {
      type: Object,
      required: true,
    },
    globalFilter: {
      type: Object,
      required: true,
    },
    widgetFilter: {
      type: Object,
      default: undefined,
    },
  },
  data() {
    return {
      // json string fed to amChart.   Used to build the look and feel of the chart
      jsonConfig: {},
    }
  },
  computed: {
    getChartColors: get('settings/chartColors'), // get a list of default chart colors
    amChartTypes: get('dashboard/amChartTypes'), // get a chartType to tell amchart what chart to use
    amSeriesTypes: get('dashboard/amSeriesTypes'), // get a seriesType to tell amchart what series to use
    // css - unimportant
    getStyle() {
      return 'width: 100%; height: 100%;'
    },
  },
  watch: {
    // watch if filter changed.   if yes then get the data again with the new filter.
    globalFilter() {
      console.log('filter updated')
      this.fetchGraphqlData()
    },
  },
  async mounted() {
    // method that builds the json string for amchart.
    await this.buildChartConfig(this.widget)

    // this is where the chart is built.
    // jsonConfig is the info that controls every aspect of the chart including data.
    // chartid_ + instance is a unique ID on the page for this chart.
    // amChartType retuirns XYChart or PieChart.  Tells amchart which type of chart will be rendered.
    // ISSUE HERE:  jsongConfig is setup as reactive, but when it changes it doesn cause this chart to re-render.
    var chart = am4core.createFromConfig(
      this.jsonConfig,
      'chartid_' + this.widget.instance,
      this.amChartTypes[this.widget.type]
    )
    console.log(chart)
  },

  //  removes charts when leaving removing this component
  beforeDestroy() {
    console.log('dispose')
    if (this.chart) {
      this.chart.dispose()
    }
  },
  methods: {
    // functions that call the API and returns data.
    callGraphql: call('dashboard/callGraphql'),
    async fetchGraphqlData() {
      const response = await this.callGraphql({
        module: 'logstats/bysource',
        widget: this.widget,
        globalFilter: this.globalFilter,
        widgetFilter: this.widgetFilter,
      })
      return response
    },

    /********************************/
    /* Build Widget                 */
    /********************************/

    // function that builds the jsonConfig JSON Object.
    async buildChartConfig(widget) {
      // TODO: the following are needed for the widget config.  Hardcoded for now.
      const xField = 'sending_host'
      const yValue = 'total_num_events'
      const xAxisTitle = 'Axis X Title'
      const yAxisTitle = 'Axis Y Title'
      console.log(xField, yValue)

      const config = []
      // defaults section
      const defaults = {
        responsive: { enabled: 'true' },
        cursor: {},
        scrollbarX: {},
        paddingRight: 20,
      }
      console.log('defaults', defaults)
      Object.assign(config, defaults)

      // colors section
      const colors = { colors: { list: Object.values(this.getChartColors) } }
      Object.assign(config, colors)

      // data section
      const tableData = await this.fetchGraphqlData()
      Object.assign(config, { data: tableData })

      // Series section
      const series = {
        series: [
          {
            type: this.amSeriesTypes[this.widget.type],
            dataFields: { valueY: yValue, categoryX: xField },
            columns: {
              tooltipText: '{valueY.value}',
            },
          },
        ],
      }

      Object.assign(config, series)

      // Axis Section
      const xAxis = {
        xAxes: [
          {
            type: 'CategoryAxis',
            dataFields: {
              category: xField,
            },
            title: { text: xAxisTitle },
          },
        ],
      }

      Object.assign(config, xAxis)
      const yAxis = {
        yAxes: [
          {
            type: 'ValueAxis',
            title: { text: yAxisTitle },
          },
        ],
      }

      Object.assign(config, yAxis)
      console.log('Axis:', xAxis, yAxis)

      // legend Section
      const legend = { legend: {} }
      Object.assign(config, legend)

      console.log('json string:', config)
      this.jsonConfig = config
    },
  },
}
</script>

<template>
  <v-container style="width: 100%; height: 100%;" class="pa-1">
    Inner Widget {{ widget }}
    <div>
      <base-widget-system-bar
        :title="widget.title"
        :item-index="widget.instance"
      />
    </div>
    <div
      :id="'chartid_' + widget.instance"
      style="width: 90%; height: 90%;  margin: auto;"
    >
    </div>
  </v-container>
</template>
