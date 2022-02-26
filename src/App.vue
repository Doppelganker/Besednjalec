<script setup lang="ts">
import GameWindow from './components/GameWindow.vue'
</script>

<template>
  <div class="bg-yellow-50 h-screen">
    <header
      class="font-bold text-center text-2xl text-cyan-600 border-b-4 pb-2"
    >
      Besednjalec
    </header>

    <main class="m-4">
      <GameWindow />
    </main>
  </div>
</template>
