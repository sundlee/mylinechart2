<template>
  <div class="lineChart">
    <line-chart ref="lineChart" :chart-data="chartData" :options="options" />
    <button @click="addData">점 추가</button>
    <button @click="removeData">점 삭제</button>
    <button @click="addDataset">라인 추가</button>
    <button @click="removeDataset">라인 삭제</button>
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
      chartData: {},
      datasets: [],
      options: {
        responsive: true,
        maintainAspectRatio: false,
        // title: {
        //   display: true,
        //   text: 'Custom Chart Title',
        // },
        legend: {
          display: true,
          position: 'top',
          align: 'start',
          // labels: {
          //   fontColor: 'rgb(255, 99, 132)',
          // },
        },
        // layout: {
        //   padding: {
        //     left: 50,
        //     right: 50,
        //     top: 50,
        //     bottom: 50,
        //   },
        // },
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
          mode: 'index',
          intersect: false,
        },
        // elements: {
        //   point: {
        //     pointStyle: 'circle',
        //   },
        // },
        // plugins: {
        //   deferred: {
        //     xOffset: '50%',
        //     delay: 1000,
        //   },
        //   crosshair: {
        //     sync: {
        //       enabled: false,
        //     },
        //     zoom: {
        //       enabled: false,
        //     },
        //   },
        // },
      },
    };
  },
  mounted() {
    this.load();
  },
  methods: {
    getChartColor() {
      const colorNames = Object.keys(chartColors);
      const colorName = colorNames[this.chartData.datasets.length % colorNames.length];
      const newColor = chartColors[colorName];
      // console.log(`getChartColor() - colorName: ${colorName}, newColor: ${newColor}`);
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
        fill: false,
        data: [],
      };
      for (let i = 0; i < this.chartData.labels.length; i++) {
        newDataset.data.push(this.randomScalingFactor());
      }
      // if (this.chartData.datasets.length % 5 === 0) {
      //   const lineChart = document.getElementById('line-chart');
      //   lineChart.style.height = `${400 + this.chartData.datasets.length * 5}px`;
      //   this.$refs.lineChart.update();
      // }
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
.customFields {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  margin-top: 50px;
}
.radioBtnSeries {
  display: flex;
  justify-content: center;
}
.radioBtnSeries label {
  margin-right: 30px;
}
</style>
