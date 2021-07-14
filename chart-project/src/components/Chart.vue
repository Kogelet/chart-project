<template>
  <div>
    <apexchart :options="chartOptions" :series="series"></apexchart>
  </div>
</template>

<script>
import VueApexCharts from "vue-apexcharts";
export default {
  components: {
    apexchart: VueApexCharts,
  },
  data() {
    return {
      chartOptions: {
        chart: {
          width: "100%",
          type: "line",
        },
        xaxis: {
          type: "datetime",
          labels: {
            format: "MMM 'yy",
          },
        },
      },
      series: [],
    };
  },
  methods: {
    async updateChart() {
      const response = await fetch(
        "https://mocki.io/v1/d6797405-4dad-4d36-b05b-3c29165fccab"
      );

      const data = await response.json();

      console.log(data);

      this.chartOptions = {
        chart: {
          width: "100%",
          type: "line",
          stacked: false,
        },
        xaxis: {
          type: "datetime",
          labels: {
            format: "MMM 'yy",
          },
        },
        stroke: {
          width: [5, 0, 2, 2],
        },
        yaxis: [
          {
            seriesName: this.instrument,
            opposite: false,
            decimalsInFloat: 4,
            tickAmount: 5,
            max: (max) => {
              if (max) {
                const numDec = max.toString().split(".")[1].length;
                let newMax =
                  Math.ceil(max * Math.pow(10, numDec - 2)) /
                  Math.pow(10, numDec - 2);
                return newMax;
              }
              return max;
            },
            min: (min) => {
              //console.log(this.chartOptions["series"][0]["data"]);
              min = Math.min(...data.prices.map((item) => item[1]));
              return min;
            },
            axisTicks: {
              show: true,
            },
            axisBorder: {
              show: true,
              color: "#008FFB",
            },
            labels: {
              style: {
                colors: "#008FFB",
              },
            },
            title: {
              text: this.instrument,
              style: {
                fontSize: "26px",
                color: "#008FFB",
              },
            },
            tooltip: {
              enabled: true,
            },
          },
          {
            seriesName: "Noncom Net Positions",
            opposite: true,
            decimalsInFloat: 0,
            axisTicks: {
              show: true,
            },
            axisBorder: {
              show: true,
              color: "#00E396",
            },
            labels: {
              style: {
                colors: "#00E396",
              },
            },
            title: {
              text: "Noncom Net Positions",
              style: {
                fontSize: "18px",
                color: "#00E396",
              },
            },
          },
          { show: false, decimalsInFloat: 0 },
          { show: false, decimalsInFloat: 0 },
        ],
      };

      this.series = [
        {
          name: data.instrument,
          type: "line",
          data: data.prices,
        },
        {
          name: "Noncom Net Positions",
          type: "area",
          data: data.noncommercial_net,
        },
        {
          name: "Noncom Long",
          type: "line",
          data: data.noncommercial_long,
        },
        {
          name: "Noncom Short",
          type: "line",
          data: data.noncommercial_short,
        },
      ];
    },
  },
  created() {
    this.updateChart();
  },
};
</script>
