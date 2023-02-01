<template>
  <svg xmlns="http://www.w3.org/2000/svg" height="48" width="48" v-if="!up">
    <path
      d="m24 30.75-12-12 2.15-2.15L24 26.5l9.85-9.85L36 18.8Z"
      fill="white"
    />
  </svg>
  <svg xmlns="http://www.w3.org/2000/svg" height="48" width="48" v-if="up">
    <path
      d="M14.15 30.75 12 28.6l12-12 12 11.95-2.15 2.15L24 20.85Z"
      fill="white"
    />
  </svg>
</template>

<script setup lang="ts">
defineProps<{ up?: boolean }>();
</script>

<style scoped lang="scss">
.up {
  transform: rotate(180deg);
}
</style>
