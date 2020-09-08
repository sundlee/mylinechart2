<template>
  <div class="lineChart">
    <button @click="addData">점 추가</button>
    <button @click="removeData">점 삭제</button>
    <button @click="addDataset">라인 추가</button>
    <button @click="removeDataset">라인 삭제</button>
    <line-chart ref="lineChart" :styles="chartStyles" :chart-data="chartData" :options="options" />
  </div>
</template>

<script>
import moment from 'moment';
import LineChart from '@/components/LineChart.vue';
import { ChartDataMixin } from '../mixins/chartDataMixins';
import { chartColors } from '../utils/utils';

export default {
  name: 'Home',
  mixins: [ChartDataMixin],
  components: {
    LineChart,
  },
  data() {
    return {
      height: 400,
      chartData: {},
      datasets: [],
      options: {
        responsive: true,
        maintainAspectRatio: false,
        legend: {
          display: true,
          position: 'top',
          align: 'start',
        },
        scales: {
          x: {
            type: 'time',
            display: true,
            scaleLabel: {
              display: true,
              labelString: 'Date',
            },
            ticks: {
              major: {
                enabled: true,
              },
              font: (context) => {
                if (context.tick && context.tick.major) {
                  return {
                    style: 'bold',
                    color: '#FF0000',
                  };
                }
              },
            },
          },
          y: {
            display: true,
            scaleLabel: {
              display: true,
              labelString: 'value',
            },
          },
        },
        tooltips: {
          mode: 'point',
          intersect: true,
        },
      },
    };
  },
  created() {
    this.chartData.datasets = [];
  },
  mounted() {
    this.load();
  },
 computed: {
    chartStyles () {
      return {
        height: `${400 + this.chartData.datasets.length * 5}px`,
        position: 'relative'
      }
    }
  },
  methods: {
    getChartColor() {
      const colorNames = Object.keys(chartColors);
      const colorName = colorNames[this.chartData.datasets.length % colorNames.length];
      const newColor = chartColors[colorName];
      return newColor;
    },
    load() {
      this.chartData = {
        labels: [
          this.newDateString(0),
          this.newDateString(2),
          this.newDateString(4),
          this.newDateString(5),
        ],
        datasets: [{
          label: 'GET /my/v1.0/aaa/bbb/ccc/ddd1',
          borderColor: chartColors.blue,
          backgroundColor: chartColors.blue,
          cubicInterpolationMode: 'monotone',
          fill: false,
          data: [
            this.randomScalingFactor(),
            this.randomScalingFactor(),
            this.randomScalingFactor(),
            this.randomScalingFactor(),
          ],
        }, {
          label: 'GET /my/v1.0/aaa/bbb/ccc/ddd2',
          borderColor: chartColors.green,
          backgroundColor: chartColors.green,
          cubicInterpolationMode: 'monotone',
          fill: false,
          data: [
            this.randomScalingFactor(),
            this.randomScalingFactor(),
            this.randomScalingFactor(),
            this.randomScalingFactor(),
          ],
        }],
      };
    },
    newDateString(days) {
      return moment().add(days, 'd').format('hh:mm:ss');
    },
    randomScalingFactor() {
      return Math.floor(Math.random() * 60) + 1;
    },
    addData() {
      if (this.chartData.datasets.length > 0) {
        this.chartData.labels.push(this.newDateString(this.chartData.datasets[0].data.length + 2));
        this.chartData.datasets.forEach((d) => {
          d.data.push(this.randomScalingFactor());
        });
        this.$refs.lineChart.update();
      }
    },
    removeData() {
      if (this.chartData.datasets.length > 0) {
        this.chartData.labels.pop();
        this.chartData.datasets.forEach((d) => {
          d.data.pop();
        });
        this.$refs.lineChart.update();
      }
    },
    addDataset() {
      const newDataset = {
        label: `GET /my/v1.0/aaa/${this.chartData.datasets.length}`,
        borderColor: this.getChartColor(),
        backgroundColor: this.getChartColor(),
        cubicInterpolationMode: 'monotone',
        fill: false,
        data: [],
      };
      for (let i = 0; i < this.chartData.labels.length; i++) {
        newDataset.data.push(this.randomScalingFactor());
      }
      this.chartData.datasets.push(newDataset);
      this.$refs.lineChart.update();
    },
    removeDataset() {
      this.chartData.datasets.splice(0, 1);
      this.$refs.lineChart.update();
    },
  },
};
</script>

<style lang="scss">
.lineChart {
  display: block;
  margin-left: auto;
  margin-right: auto;
  width: 80%;
  height: 400px;

  button {
    margin-left: 5px;
  }
}
</style>
