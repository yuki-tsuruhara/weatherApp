<template>
  <div class="w-[240px] h-full bg-white/20 backdrop-blur p-6 rounded-l-lg">
    <div class="flex items-center justify-start gap-4 cursor-pointer">
      <div class="relative w-full">
        <input
          type="text"
          class="bg-transparent border-b-[1px] w-full outline-none p-1"
          v-model="inputLocation"
          @keydown.enter="searchLocation"
        />
        <span class="material-symbols-outlined absolute top-[5px] right-0"> search </span>
      </div>
    </div>
    <div class="mt-10 text-center">
      <h2 class="text-5xl">{{ (weatherData.list[0].main.temp / 10).toFixed(1) }}°</h2>
    </div>
    <div class="mt-5 flex items-center justify-between">
      <h3 class="text-2xl">{{ weatherData.list[0].main.humidity }}%</h3>
      <p>Wind: {{ weatherData.list[0].wind.speed }} m/s</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue';
import type { WeatherData } from '../../types/api';
const props = defineProps<{
  weatherData: WeatherData;
}>();

const emit = defineEmits(['searchLocation']);

const inputLocation = ref<string>('');

const searchLocation = (): void => {
  emit('searchLocation', inputLocation.value);
};

watch(props.weatherData, () => {
  inputLocation.value = props.weatherData.city.name;
});
</script>

<style lang="scss" scoped></style>
