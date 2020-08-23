<template>
  <v-layout justify-center align-top>
    <v-flex class="left-column">
      <v-layout wrap row mx-auto>
        <v-flex
          icon-wrap
          justify-center
          xs6
          v-for="(item, i) in statics"
          :key="i"
        >
          <img class="icon d-block mx-auto" :src="item.icon" />
          <label class="text-center d-block">{{ item.label }}</label>
          <span class="text-center d-block"
            >{{ item.value }}<span class="unit">{{ item.unit }}</span></span
          >
        </v-flex>
      </v-layout>
      <v-card>
        <v-card-title class="justify-center">节能减排业绩 </v-card-title>
        <v-card-text>
          <template v-for="(item, i) in score">
            <v-layout :class="`color_${++i}`" mx-0 :key="i">
              <v-flex
                ><v-avatar>{{ i }}</v-avatar>
                {{ item.name }}
              </v-flex>
              <v-spacer />
              <v-flex text-right>
                <span class="number">{{ item.number }}</span>
                <span class="unit">{{ item.unit }}</span>
              </v-flex>
            </v-layout>
          </template>
        </v-card-text>
      </v-card>
    </v-flex>
    <v-flex>
      <v-layout mx-auto row wrap>
        <v-flex class="center-column">
          <div>
            <LineChart
              className="small-chart"
              ref="lineChart"
              :chart-data="lineChartDataYear"
              :x-axis-data="xAxisDataYear"
              title="年度发电量对比"
              unit="(kW-h)"
            />
            <LineChart
              className="small-chart"
              ref="lineChart"
              :chart-data="lineChartDataHour"
              :x-axis-data="xAxisDataHour"
              title="今日各小时发电功率趋势"
              unit="(kW)"
            />
          </div>
        </v-flex>
        <v-flex class="right-column">
          <div>
            <LineChart
              className="small-chart"
              ref="lineChart"
              :chart-data="lineChartDataMonth"
              :x-axis-data="xAxisDataMonth"
              title="月发电量对比"
              unit="(kW-h)"
            />
            <LineChart
              className="small-chart"
              ref="lineChart"
              :chart-data="lineChartDataQuarter"
              :x-axis-data="xAxisDataQuater"
              title="各季度发电量对比"
              unit="(kW-h)"
            />
          </div>
        </v-flex>
      </v-layout>
      <v-flex class="full-column">
        <LineChart
          className="small-chart"
          ref="lineChart"
          :chart-data="lineChartDataDay"
          :x-axis-data="xAxisDataDay"
          title="本月每日发电趋势比较"
          unit="(kW-h)"
        />
      </v-flex>
    </v-flex>
  </v-layout>
</template>

<script>
import LineChart from "@/components/LineChart.vue";
import _ from "lodash";
export default {
  components: { LineChart },
  data() {
    return {
      statics: [
        {
          icon: require("../assets/img/icon-01@3x.png"),
          label: "日照值",
          value: "99,999",
          unit: "W/m²"
        },
        {
          icon: require("../assets/img/icon-02@3x.png"),
          label: "环境温度",
          value: "33.33",
          unit: "˚C"
        },
        {
          icon: require("../assets/img/icon-03@3x.png"),
          label: "电池温度",
          value: "33.33",
          unit: "˚C"
        },
        {
          icon: require("../assets/img/icon-04@3x.png"),
          label: "日峰值时数",
          value: "9.99",
          unit: "h"
        }
      ],
      score: [
        {
          name: "二氧化碳减排量",
          number: "999",
          unit: "kg"
        },
        {
          name: "二氧化硫减排量",
          number: "9,999",
          unit: "kg"
        },
        {
          name: "氮化物减排量",
          number: "99,999",
          unit: "kg"
        },
        {
          name: "烟尘减排量",
          number: "999,999",
          unit: "kg"
        },
        {
          name: "标准煤减排量",
          number: "9,999,999",
          unit: "kg"
        }
      ],
      lineChartDataYear: {
        expect: {
          data: [null, 300000, 260000, 260000, 190000, 200000],
          type: "line",
          color: "#ecc83c",
          label: "预计发电"
        },
        standar: {
          data: [null, 290000, 240000, 270000, 180000, 240000],
          type: "line",
          color: "#fe92a5",
          label: "标准值"
        },
        real: {
          data: [null, 280000, 250000, 280000, 160000, 290000],
          type: "bar",
          color: "#67bff4",
          barWidth: "10",
          label: "实际发电"
        }
      },
      xAxisDataYear: ["", 2016, 2017, 2018, 2019, 2020, ""],
      chartData: {
        expect: {
          data: [],
          type: "line",
          color: "#ecc83c",
          label: "预计发电",
          shadowBlur: 1,
          shadowColor: "rgba(0,0,0,0.50)",
          shadowOffsetX: 0,
          shadowOffsetY: 1
        },
        standar: {
          data: [],
          type: "line",
          color: "#fe92a5",
          label: "标准值",
          shadowBlur: 1,
          shadowColor: "rgba(0,0,0,0.50)",
          shadowOffsetX: 0,
          shadowOffsetY: 1
        },
        real: {
          data: [],
          type: "bar",
          color: "#67bff3",
          barWidth: "10",
          label: "实际发电",
          shadowBlur: 1,
          shadowColor: "rgba(0,0,0,0.50)",
          shadowOffsetX: 0,
          shadowOffsetY: 1
        }
      }
    };
  },
  computed: {
    xAxisDataC() {
      const t = [];
      return t;
    },
    lineChartDataHour() {
      let chartData = _.cloneDeep(this.chartData);

      chartData.expect.data.push(null);
      chartData.standar.data.push(null);
      chartData.real.data.push(null);
      chartData.real.type = "line";
      for (let i = 4; i < 21; i++) {
        switch (i) {
          case 5:
            chartData.expect.data.push(160);
            chartData.standar.data.push(140);
            chartData.real.data.push(130);
            break;
          case 9:
            chartData.expect.data.push(140);
            chartData.standar.data.push(140);
            chartData.real.data.push(170);
            break;
          case 12:
            chartData.expect.data.push(169);
            chartData.standar.data.push(120);
            chartData.real.data.push(110);
            break;
          case 16:
            chartData.expect.data.push(100);
            chartData.standar.data.push(90);
            chartData.real.data.push(200);
            break;
          case 19:
            chartData.expect.data.push(200);
            chartData.standar.data.push(340);
            chartData.real.data.push(220);
            break;
          default:
            break;
        }
      }
      return chartData;
    },
    xAxisDataHour() {
      const hours = [];

      hours.push("");
      for (let i = 5; i < 21; i++) {
        switch (i) {
          case 5:
          case 9:
          case 12:
          case 16:
          case 19:
            hours.push(`${i}:00`);
            break;
          default:
            break;
        }
      }
      hours.push("");
      return hours;
    },
    lineChartDataMonth() {
      let chartData = _.cloneDeep(this.chartData);

      for (let i = 0; i <= 13; i++) {
        switch (i) {
          case 0:
          case 13:
            chartData.expect.data.push(null);
            chartData.standar.data.push(null);
            chartData.real.data.push(null);
            break;

          default:
            chartData.expect.data.push(25000 * Math.random());
            chartData.standar.data.push(25000 * Math.random());
            chartData.real.data.push(25000 * Math.random());
            break;
        }
      }
      return chartData;
    },
    xAxisDataMonth() {
      const months = [];
      months.push("");
      for (let i = 1; i <= 12; i++) {
        months.push(`${i}月`);
      }
      months.push("");
      return months;
    },
    lineChartDataQuarter() {
      let chartData = _.cloneDeep(this.chartData);

      for (let i = 0; i < 5; i++) {
        switch (i) {
          case 1:
            chartData.expect.data.push(160);
            chartData.standar.data.push(140);
            chartData.real.data.push(130);
            break;
          case 2:
            chartData.expect.data.push(140);
            chartData.standar.data.push(140);
            chartData.real.data.push(170);
            break;
          case 3:
            chartData.expect.data.push(169);
            chartData.standar.data.push(120);
            chartData.real.data.push(110);
            break;
          case 4:
            chartData.expect.data.push(100);
            chartData.standar.data.push(90);
            chartData.real.data.push(200);
            break;
          default:
            chartData.expect.data.push(null);
            chartData.standar.data.push(null);
            chartData.real.data.push(null);
            break;
        }
      }
      return chartData;
    },
    xAxisDataQuater() {
      const quaters = [];
      quaters.push("");
      quaters.push("第一季度");
      quaters.push("第二季度");
      quaters.push("第三季度");
      quaters.push("第四季度");
      quaters.push("");
      return quaters;
    },
    lineChartDataDay() {
      let chartData = _.cloneDeep(this.chartData);

      const today = new Date();
      const oneDay = 1000 * 60 * 60 * 24;
      let currentDay = new Date(today * 1 - (today.getDate() - 1) * oneDay);
      let overMonth = false;
      chartData.expect.data.push(null);
      chartData.standar.data.push(null);
      chartData.real.data.push(null);
      chartData.real.type = "line";
      while (currentDay.getMonth() == today.getMonth()) {
        chartData.expect.data.push(1000 * Math.random());
        chartData.standar.data.push(1000 * Math.random());
        chartData.real.data.push(1000 * Math.random());
        currentDay = new Date(currentDay * 1 + oneDay);
      }

      chartData.expect.data.push(null);
      chartData.standar.data.push(null);
      chartData.real.data.push(null);
      return chartData;
    },
    xAxisDataDay() {
      const today = new Date();
      const oneDay = 1000 * 60 * 60 * 24;
      let currentDay = new Date(today * 1 - (today.getDate() - 1) * oneDay);
      let overMonth = false;
      const days = [];
      days.push("");
      while (currentDay.getMonth() == today.getMonth()) {
        days.push(
          `${Number(currentDay.getMonth() + 1)}/${Number(currentDay.getDate())}`
        );
        currentDay = new Date(currentDay * 1 + oneDay);
      }

      days.push("");
      return days;
    }
  }
};
</script>
