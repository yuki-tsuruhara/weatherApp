<template>
  <div class="p-[40px] bg-black/30 text-white flex-1 font-bold">
    <div class="flex flex-col justify-between h-full">
      <div>
        <div>
          <p class="text-sm">Weather Forecast</p>
          <h2 class="text-5xl mt-3">{{ weatherData.list[0].weather[0].description }}</h2>
        </div>
        <div class="mt-[40px]">
          <p>
            {{ weatherData.city.country }}, {{ weatherData.city.name }},
            {{ weatherData.list[0].dt_txt }}
          </p>
        </div>
      </div>
      <div class="mt-[40px] flex-1 mx-auto w-full max-w-[800px]">
        <canvas ref="ctx"></canvas>
      </div>
      <div>
        <ul class="flex justify-between gap-10 items-center mt-[40px]">
          <li v-for="forecast in weatherData.list" :key="forecast.dt">
            <p class="text-2xl mb-[10px]">{{ (forecast.main.temp / 10).toFixed(1) }}°</p>
            <span
              class="block h-[2px] w-full max-w-[80px]"
              :class="tempbar(forecast.main.temp / 10)"
            ></span>
            <span
              class="block h-[2px] w-full max-w-[50px] mt-[10px]"
              :class="tempbar(forecast.main.temp / 10)"
            ></span>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { Chart, registerables } from 'chart.js';
import type { WeatherData } from 'types/api';
import { computed, onMounted, reactive, watch, ref } from 'vue';

type ChartDataset = {
  borderColor?: string;
  tension?: number;
  fill?: boolean;
  data: number[];
};
type ChartData = {
  labels: string[];
  datasets: ChartDataset[];
};

const props = defineProps<{
  weatherData: WeatherData;
}>();

const ctx = ref<HTMLCanvasElement | null>(null);

const tempPerWeek = computed(() => {
  return props.weatherData.list.map((day: any) => {
    return Number((day.main.temp / 10).toFixed(1));
  });
});

let chartData = reactive<ChartData>({
  labels: [],
  datasets: []
});

const chartOptions = reactive({
  plugins: {
    legend: {
      display: false
    }
  },
  scales: {
    x: {
      display: false
    },
    y: {
      display: false
    }
  },
  responsive: true
});

watch(
  props.weatherData,
  () => {
    if (props.weatherData.list.length > 0) {
      chartData.labels = ['current', '3hour', '6hour', '9hour', '12hour', '15hour', '18hour'];
      chartData.datasets = [
        { fill: false, tension: 0.5, borderColor: 'rgb(75, 192, 192)', data: tempPerWeek.value }
      ];
    }
    updateChart();
  },
  { deep: true }
);

const tempbar = (temp: number) => {
  let num_temp = Number(temp);
  num_temp.toFixed(1);
  if (num_temp > 30) {
    return 'bg-red-600';
  } else if (20 < num_temp && num_temp <= 30) {
    return 'bg-yellow-400';
  } else if (0 < num_temp && num_temp <= 10) {
    return 'bg-sky-400';
  } else {
    return 'bg-blue-700';
  }
};

const updateChart = () => {
  if (ctx.value) {
    const existingChart = Chart.getChart(ctx.value);
    if (existingChart) {
      existingChart.destroy();
    }

    new Chart(ctx.value, {
      type: 'line',
      data: chartData,
      options: chartOptions
    });
  }
};

onMounted(() => {
  if (ctx.value) {
    Chart.register(...registerables);
    console.log({ type: 'line', data: chartData, options: chartOptions });
    new Chart(ctx.value, {
      type: 'line',
      data: chartData,
      options: chartOptions
    });
  }
});
</script>

<style lang="scss" scoped></style>
