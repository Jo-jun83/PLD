<script setup>
defineProps({
  favoriteCount: {
    type: Number,
    required: true
  },

  currentView: {
    type: String,
    required: true
  }
})

const emit = defineEmits(['navigate'])
</script>
<template>
  <header class="bg-slate-950 border-b border-slate-900">
    <div
      class="mx-auto flex max-w-6xl flex-col gap-4 px-6 py-5 sm:flex-row sm:items-center sm:justify-between"
    >
      <div class="flex items-center gap-3">
        <div
          class="flex h-9 w-9 items-center justify-center rounded-lg bg-pink-500 font-bold text-white"
        >
          M
        </div>

        <span class="text-xl font-bold text-white">
          MOVIE NIGHT
        </span>
      </div>

      <nav class="flex gap-2">
        <button
        @click="emit('navigate', 'films')"
        class="rounded-xl px-6 py-3 text-sm font-semibold"
        :class="
            currentView === 'films'
            ? 'bg-pink-500 text-white'
            : 'bg-slate-800 text-slate-300'
        "
        >
        Films
        </button>

        <button
        @click="emit('navigate', 'favorites')"
        class="rounded-xl px-6 py-3 text-sm font-semibold"
        :class="
            currentView === 'favorites'
            ? 'bg-pink-500 text-white'
            : 'bg-slate-800 text-slate-300'
        "
        >
        ♥ Favoris {{ favoriteCount }}
        </button>
      </nav>
    </div>
  </header>
</template>