<script setup lang="ts">
import PageWithNoTransition from "../components/PageWithNoTransition.vue";
</script>

<template>
  <div class="slidev-layout center h-full grid place-content-center">
    <div class="my-auto flex gap-4">
      <section class="w-96 h-96 p-2 flex flex-col justify-center">
        <slot/>
      </section>
      <section class="w-96 h-96 border border-white overflow-auto">
        <PageWithNoTransition/>
      </section>
    </div>
  </div>

</template>
