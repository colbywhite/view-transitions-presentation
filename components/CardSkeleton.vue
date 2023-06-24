<script setup lang="ts">
const props = defineProps({
  title: {
    default: 'Title'
  }
})

defineEmits(['selected'])
</script>

<template>
  <h4 class="mb-2 cursor-pointer" @click="$emit('selected', props.title)">{{ props.title }}</h4>
  <div role="status" class="max-w-sm mb-8">
    <div class="h-2.5 bg-gray-200 rounded-full dark:bg-gray-700 w-48 mb-4"></div>
    <div class="h-2 bg-gray-200 rounded-full dark:bg-gray-700 max-w-[360px] mb-2.5"></div>
    <div class="h-2 bg-gray-200 rounded-full dark:bg-gray-700 mb-2.5"></div>
    <div class="h-2 bg-gray-200 rounded-full dark:bg-gray-700 max-w-[330px] mb-2.5"></div>
    <div class="h-2 bg-gray-200 rounded-full dark:bg-gray-700 max-w-[300px] mb-2.5"></div>
    <div class="h-2 bg-gray-200 rounded-full dark:bg-gray-700 max-w-[360px]"></div>
  </div>
</template>

<style scoped>

</style>
