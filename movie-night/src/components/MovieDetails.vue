<script setup>
defineProps({
  movie: {
    type: Object,
    required: true
  }
})

const emit = defineEmits([
  'close',
  'toggle-favorite'
])
</script>

<template>
  <div
    class="fixed inset-0 z-50 flex items-center justify-center bg-black/80 px-4"
  >
    <div
      class="relative w-full max-w-3xl rounded-2xl border border-slate-700 bg-slate-900 p-6 shadow-2xl"
    >
      <!-- Croix -->
      <button
        @click="emit('close')"
        class="absolute right-4 top-4 z-10 flex h-9 w-9 items-center justify-center rounded-full bg-slate-800 text-white hover:bg-slate-700"
      >
        ✕
      </button>

      <!-- Contenu -->
      <div class="flex flex-col gap-6 md:flex-row">
        <!-- Image -->
        <img
          :src="movie.image"
          :alt="movie.title"
          class="w-full rounded-xl object-cover md:w-72"
        >

        <!-- Informations -->
        <div class="flex flex-1 flex-col">
          <h2 class="pr-10 text-2xl font-bold text-white">
            {{ movie.title }}
          </h2>

          <p class="mt-2 text-sm text-slate-400">
            {{ movie.year }}
            <span class="mx-2">•</span>
            {{ movie.category }}
            <span class="mx-2">•</span>
            {{ movie.duration }}
          </p>

          <p class="mt-5 font-semibold text-yellow-400">
            ★ {{ movie.rating }} / 10
          </p>

          <p class="mt-5 leading-7 text-slate-300">
            {{ movie.description }}
          </p>

          <button
            @click="emit('toggle-favorite', movie.id)"
            class="mt-8 w-full rounded-xl bg-pink-500 px-5 py-3 font-semibold text-white hover:bg-pink-400"
          >
            ♥
            {{ movie.favorite ? 'Retirer des favoris' : 'Ajouter aux favoris' }}
          </button>

          <button
            @click="emit('close')"
            class="mt-4 w-full rounded-xl border border-slate-700 bg-slate-800 px-5 py-3 font-semibold text-white hover:bg-slate-700"
          >
            Fermer
          </button>
        </div>
      </div>
    </div>
  </div>
</template>