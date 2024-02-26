<template>
  <GChart :type="chartType" :data="chartData" :options="chartOptions" />
</template>

<script>
import { GChart } from 'vue-google-charts';

export default {
  props: {
    chartType:String,
    chartData:Array,
    chartOptions:Object
  },
  data: function () {
    return {
    };
  },
  created() {
  },
  computed: {
  },
  components: {
    GChart: GChart,
  },
  mounted() {
  },
  watch: {
    chartData: {
      immediate: false,
      handler(newVal, oldVal){
        if(newVal != oldVal){
          this.reloadGChart(newVal, this.chartOptions, this.chartType);
        }
      }
    },
    reloadGChart(data, options, type) {
      return () =>
        (GChart, {
          data,
          options,
          type,
        });
    },
  },
  methods: {
  },
};
</script>
