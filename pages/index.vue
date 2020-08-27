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
import axios from "axios";
import mockData from "../static/mock.json";
export default {
  components: { LineChart },
  data() {
    return {
      rawEverything: null,
    };
  },
  computed: {
    statics() {
      const statics = [
        {
          icon: require("../assets/img/icon-01@3x.png"),
          label: "日照值",
        },
        {
          icon: require("../assets/img/icon-02@3x.png"),
          label: "环境温度",
        },
        {
          icon: require("../assets/img/icon-03@3x.png"),
          label: "电池温度",
        },
        {
          icon: require("../assets/img/icon-04@3x.png"),
          label: "日峰值时数",
        },
      ];
      if (!this.rawEverything) {
        return statics;
      }
      _.each(statics, (item, i) => {
        item.value = this.rawEverything.statics[i].value;
        item.unit = this.rawEverything.statics[i].unit;
      });

      return statics;
    },
    score() {
      const score = [
        {
          name: "二氧化碳减排量",
        },
        {
          name: "二氧化硫减排量",
        },
        {
          name: "氮化物减排量",
        },
        {
          name: "烟尘减排量",
        },
        {
          name: "标准煤减排量",
        },
      ];
      if (!this.rawEverything) {
        return score;
      }
      _.each(score, (item, i) => {
        item.number = this.rawEverything.score[i].number;
        item.unit = this.rawEverything.score[i].unit;
      });
      return score;
    },
    renderData() {
      const statics = _.each(this.statics, (element) => {
        delete element.icon;
        delete element.label;
      });
      const score = _.each(this.score, (element) => {
        delete element.name;
      });
      return {
        score,
        statics,
        dataYear: this.lineChartDataYear,
        xAxisYear: this.xAxisDataYear,
        dataHour: this.lineChartDataHour,
        xAxisHour: this.xAxisDataHour,
        dataMonth: this.lineChartDataMonth,
        xAxisMonth: this.xAxisDataMonth,
        dataQuarter: this.lineChartDataQuarter,
        xAxisQuater: this.xAxisDataQuater,
        dataDay: this.lineChartDataDay,
        xAxisDay: this.xAxisDataDay,
      };
    },
    lineChartDataYear() {
      return this.rawEverything ? this.rawEverything.dataYear : null;
    },
    xAxisDataYear() {
      return this.rawEverything ? this.rawEverything.xAxisYear : [];
    },
    lineChartDataHour() {
      return this.rawEverything ? this.rawEverything.dataHour : null;
    },
    xAxisDataHour() {
      return this.rawEverything ? this.rawEverything.xAxisHour : [];
    },
    lineChartDataMonth() {
      return this.rawEverything ? this.rawEverything.dataMonth : null;
    },
    xAxisDataMonth() {
      return this.rawEverything ? this.rawEverything.xAxisMonth : [];
    },
    lineChartDataQuarter() {
      return this.rawEverything ? this.rawEverything.dataQuarter : null;
    },
    xAxisDataQuater() {
      return this.rawEverything ? this.rawEverything.xAxisQuater : [];
    },
    lineChartDataDay() {
      return this.rawEverything ? this.rawEverything.dataDay : null;
    },
    xAxisDataDay() {
      return this.rawEverything ? this.rawEverything.xAxisDay : [];
    },
    // statics()
  },
  async created() {
    // console.log(process.env);
    await axios
      .get("mock.json")
      .then((resp) => {
        this.rawEverything = resp.data;
        this.$forceUpdate();
      })
      .catch(() => {
        this.rawEverything = mockData;
      });
    // console.log(rawEverything);
  },
};
</script>
