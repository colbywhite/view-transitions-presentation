<script setup lang="ts">
import {ref} from 'vue'
import CardSkeleton from "./CardSkeleton.vue";
import FullCardSkeleton from "./FullCardSkeleton.vue";

const selectedArtist = ref(undefined as string | undefined)
const showList = ref(true)

function transitionToArtist(artist: string) {
  selectedArtist.value = artist
  showList.value = false
}

function transitionToList() {
  showList.value = true
  setTimeout(() => selectedArtist.value = undefined, 2000)
}
</script>

<template>
  <section class="w-full h-full relative">
    <div :class="{ 
      'opacity-0': !showList, 'z-0': !showList,
       'opacity-100': showList, 'z-10': showList, 
    }" class="absolute p-2 w-full">
      <h3 class="mb-4">Artist list</h3>
      <CardSkeleton @selected="transitionToArtist" title="Jill Scott"/>
      <CardSkeleton @selected="transitionToArtist" title="Maxwell"/>
      <CardSkeleton @selected="transitionToArtist" title="Erykah Badu"/>
    </div>
    <div :class="{ 
      'opacity-0': showList, 'z-0': showList,
       'opacity-100': !showList, 'z-10': !showList, 
    }" class="absolute p-2 w-full">
      <FullCardSkeleton :title="selectedArtist" @back-clicked="transitionToList"/>
    </div>
  </section>
</template>

<style scoped>
div {
  transition: opacity 2s;
}
</style>
