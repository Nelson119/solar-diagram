<template>
  <div :class="className">
    <v-layout>
      <v-flex>
        <h3>
          {{ title }}<small>{{ unit }}</small>
        </h3>
      </v-flex>
      <v-flex align-end text-right>
        <div class="legend">
          <img
            v-if="chartData && chartData.real && chartData.real.type == 'bar'"
            src="../assets/img/chartsign-a@3x.png"
          />
          <img v-else src="../assets/img/chartsign-d@3x.png" />
          <span>实际发电</span>
        </div>
        <div class="legend">
          <img src="../assets/img/chartsign-b@3x.png" />
          <span>预计发电</span>
        </div>
        <div class="legend">
          <img src="../assets/img/chartsign-c@3x.png" />
          <span>标准值</span>
        </div>
      </v-flex>
    </v-layout>
    <div ref="chart" :style="{ height: height, width: width }" />
  </div>
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
      type: Object
    },
    xAxisData: {
      type: Array
    },
    title: {
      type: String,
      required: true
    },
    unit: {
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
        // console.log("this.chartData", this.chartData);
        if (this.chartData && this.xAxisData) {
          this.setOptions(val);
          this.$forceUpdate();
        }
      }
    }
  },
  computed: {},
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
      this.chart = echarts.init(this.$refs.chart, "macarons");
      this.setOptions(this.chartData);
    },
    setOptions(lineChartData = {}) {
      const series = [];
      const labels = [];
      const data = [];
      for (const kind in lineChartData) {
        series.push({
          name: lineChartData[kind].label,
          smooth: true,
          type: lineChartData[kind].type,
          itemStyle: {
            normal: {
              color: lineChartData[kind].color,
              borderColor: lineChartData[kind].color,
              symbol: "circle",
              symbolSize: 0,
              // borderWidth: 5,
              lineStyle: {
                color: lineChartData[kind].color,
                width: 2,
                shadowBlur: 1,
                shadowColor: "rgba(0,0,0,0.50)",
                shadowOffsetX: 0,
                shadowOffsetY: 1
              },
              areaStyle: {
                color: "transparent"
              },
              ...(function(){
                return lineChartData[kind].type == 'line'? {
                  borderWidth: 5,
                }:{};
              }()),
              ...(function(){
                return lineChartData[kind].type == 'bar'? {
                  shadowBlur: 0 ,
                  shadowColor: 'rgba(0, 0, 0, 0.33) ',
                  shadowOffsetX: 1 ,
                  shadowOffsetY: 1 ,
                  borderWidth: 0,
                }:{};
              }())
            }
          },
          data: [null, ...lineChartData[kind].data],
          animationDuration: 2800,
          animationEasing: "quadraticOut",
          barWidth: lineChartData[kind].barwidth,
          barMaxWidth: lineChartData[kind].barwidth
        });
        labels.push(lineChartData[kind].label);
      }
      if (this.chart) {
        this.chart.setOption({
          xAxis: {
            data: ["", ...this.xAxisData, ""],
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
                color: "#9b9b9b"
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
            show: false,
            trigger: "axis",
            axisPointer: {
              type: "cross"
            },
            padding: [5, 10],
            textStyle: {
              color: "#9b9b9b"
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
                color: "#9b9b9b"
              }
            }
          },
          legend: {
            show: false,
            data: labels,
            right: 0,
            textStyle: {
              color: "#9b9b9b",
              fontFamily: "microsoft yahei"
            }
          },
          series,
          title: {
            show: false,
            text: this.title,
            link: "http://echarts.baidu.com/option.html#title.link",
            textStyle: {
              color: "#9b9b9b",
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
  }
};
</script>
