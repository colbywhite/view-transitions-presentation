<script setup lang="ts">
import {ref} from 'vue'
import CardSkeleton from "./CardSkeleton.vue";
import FullCardSkeleton from "./FullCardSkeleton.vue";

const selectedArtist = ref(undefined as string | undefined)
const transitioning = ref(false)

function select(artist: string | undefined) {
  transitioning.value = true
  setTimeout(() => {
    selectedArtist.value = artist
    transitioning.value = false
  }, 1500)
}

function unselect() {
  select(undefined)
}
</script>

<template>
  <div v-if="transitioning" class="w-full h-full bg-white p-2">
  </div>
  <div v-else-if="selectedArtist === undefined" class="p-2">
    <h3 class="mb-4">Artist list</h3>
    <CardSkeleton @selected="select" title="Jill Scott"/>
    <CardSkeleton @selected="select" title="Maxwell"/>
    <CardSkeleton @selected="select" title="Erykah Badu"/>
  </div>
  <div v-else class="p-2">
    <FullCardSkeleton :title="selectedArtist" @back-clicked="unselect"/>
  </div>
</template>

<style scoped>

</style>
