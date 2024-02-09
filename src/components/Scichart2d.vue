<script>
import {
  DateTimeNumericAxis,
  EllipsePointMarker, FastBubbleRenderableSeries,
  FastLineRenderableSeries,
  NumberRange,
  NumericAxis,
  SciChartJsNavyTheme,
  SciChartSurface, SweepAnimation,
  XyDataSeries, XyzDataSeries
} from 'scichart';
import axios from 'axios';

async function initSciChart(data) {

  const { sciChartSurface, wasmContext } = await SciChartSurface.create('scichart-root', {
    theme: new SciChartJsNavyTheme(),
    title: 'SciChart.js First Chart',
    titleStyle: { fontSize: 22 }
  });

  const growBy = new NumberRange(1000, 0.1);
  sciChartSurface.xAxes.add(new DateTimeNumericAxis(wasmContext, { axisTitle: '', growBy }));
  sciChartSurface.yAxes.add(new NumericAxis(wasmContext, { axisTitle: '', growBy }));

  console.log(data);
  const xValues = data.map(({ dt })=>dt);
  const yValues = data.map(({ price })=>price);
  const zValues = data.map(({ amount })=>amount);
  console.log(xValues)

  sciChartSurface.renderableSeries.add(new FastLineRenderableSeries(wasmContext, {

    stroke: 'steelblue',
    strokeThickness: 3,
    dataSeries: new XyzDataSeries(wasmContext, {
      xValues,
      yValues,
      zValues
    }),
    pointMarker: new EllipsePointMarker(wasmContext, { width: 11, height: 11, fill: '#fff' }),
    animation: new SweepAnimation({ duration: 300, fadeEffect: true })
  }));

  return sciChartSurface;
}

export default {
  mounted() {
    this.makeApiRequest().then(
        ({ data }) => this.chartInitializationPromise = initSciChart(data)
    )
  },
  data() {
    return {
      chartInitializationPromise: undefined,
    };
  },
  beforeUnmount() {
    console.log('SciChart2d.vue beforeUnmount');
    this.chartInitializationPromise.then((sciChartSurface) => {
      console.log('..deleting sciChartSurface');
      sciChartSurface.delete();
    });
  },
  name: 'scichart2d',
  props: {
    msg: String
  },
  methods: {
    getBasicAuthHeader() {
      const username = 'frontend2024';
      const password = 'test';
      const base64Credentials = btoa(`${ username }:${ password }`);
      return `Basic ${ base64Credentials }`;
    },
    async makeApiRequest() {
      const axiosConfig = {
        headers: {
          Authorization: this.getBasicAuthHeader(),
          'Content-Type': 'application/json',
        },
      };

      return axios.get('https://rest.statica.pl/rest/stockquotes/gpw:PLKGHM000017?type=trades&dt_from=2010-01-01&limit=10000', axiosConfig);
    },

  }
};
</script>
<template>
  <div class="hello">
    <div id="scichart-root" style="width: 600px; height: 400px; margin: auto"></div>
  </div>
</template>
<style scoped>
</style>
