<template>
  <div
    class="absolute z-10 top-[20px] left-[50%] translate-x-[-50%] bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded"
    role="alert"
  >
    <strong>{{ alertText }}</strong>
  </div>
</template>

<script setup lang="ts">
defineProps<{
  alertText: string;
}>();
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
