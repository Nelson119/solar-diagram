<template>
  <div :class="className" :style="{ height: height, width: width }" />
</template>

<script>
import echarts from "echarts";
require("echarts/theme/royal"); // echarts theme
import resize from "./mixins/resize";

export default {
  mixins: [resize],
  props: {
    className: {
      type: String,
      default: "chart"
    },
    width: {
      type: String,
      default: "100%"
    },
    height: {
      type: String,
      default: "100%"
    },
    autoResize: {
      type: Boolean,
      default: true
    },
    chartData: {
      type: Object,
      required: true
    },
    xAxisData: {
      type: Array,
      required: true
    },
    title: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      chart: null
    };
  },
  watch: {
    chartData: {
      deep: true,
      handler(val) {
        this.setOptions(val);
      }
    }
  },
  mounted() {
    this.$nextTick(() => {
      this.initChart();
    });
  },
  beforeDestroy() {
    if (!this.chart) {
      return;
    }
    this.chart.dispose();
    this.chart = null;
  },
  methods: {
    initChart() {
      this.chart = echarts.init(this.$el, "macarons");
      this.setOptions(this.chartData);
    },
    setOptions(lineChartData = {}) {
      const series = [];
      const labels = [];
      const data = [];
      for (const kind in lineChartData) {
        series.push({
          name: lineChartData[kind].label,
          smooth: false,
          type: lineChartData[kind].type,
          itemStyle: {
            normal: {
              color: lineChartData[kind].color,
              lineStyle: {
                color: lineChartData[kind].color,
                width: 2
              },
              areaStyle: {
                color: "transparent"
              }
            }
          },
          data: lineChartData[kind].data,
          animationDuration: 2800,
          animationEasing: "quadraticOut",
          barWidth: lineChartData[kind].barwidth,
          barMaxWidth: lineChartData[kind].barwidth
        });
        labels.push(lineChartData[kind].label);
      }
      this.chart.setOption({
        xAxis: {
          data: this.xAxisData,
          boundaryGap: false,
          axisTick: {
            show: false
          },
          nameTextStyle: {
            color: "#9b9b9b",
            fontFamily: "microsoft yahei"
          },
          axisLine: {
            lineStyle: {
              color: "#fff"
            }
          }
        },
        grid: {
          left: 10,
          right: 10,
          bottom: 20,
          top: 30,
          containLabel: true
        },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "cross"
          },
          padding: [5, 10],
          textStyle: {
            color: "#fff"
          }
        },
        yAxis: {
          axisTick: {
            show: false
          },
          nameTextStyle: {
            color: "#9b9b9b",
            fontFamily: "microsoft yahei"
          },
          axisLine: {
            lineStyle: {
              color: "#fff"
            }
          }
        },
        legend: {
          data: labels,
          right: 0,
          textStyle: {
            color: "#fff",
            fontFamily: "microsoft yahei"
          }
        },
        series,
        title: {
          text: this.title,
          link: "http://echarts.baidu.com/option.html#title.link",
          textStyle: {
            color: "#fff",
            fontFamily: "microsoft yahei",
            height: 20,
            lineHeight: 20,
            fontSize: `${(24 / 1920) * 100}vw`
          }
        },
        axisPointer: {
          link: { xAxisIndex: "all" },
          label: {
            backgroundColor: "rgba(0,0,0,0.6)"
          }
        }
      });
    }
  }
};
</script>
