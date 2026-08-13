<script setup>
defineProps({
  movie: {
    type: Object,
    required: true
  }
})

const emit = defineEmits([
  'toggle-favorite',
  'show-details'
])
</script>

<template>
  <article
    class="overflow-hidden rounded-xl bg-slate-900 transition duration-200 hover:-translate-y-1 hover:shadow-xl"
    >
    <img
      :src="movie.image"
      :alt="movie.title"
      class="h-72 w-full object-cover"
    >

    <div class="p-4">
      <h2 class="text-lg font-bold text-white">
        {{ movie.title }}
      </h2>

      <p class="mt-2 text-sm text-slate-400">
        {{ movie.year }} · {{ movie.category }}
      </p>

      <p class="mt-2 text-sm text-yellow-400">
        ⭐ {{ movie.rating }}
      </p>

      <div class="mt-4 flex items-center gap-2">
        <button
          @click="emit('show-details', movie)"
          class="flex-1 rounded-lg bg-pink-500 px-4 py-2 text-sm font-semibold text-white"
        >
          Voir le détail
        </button>

        <button
        @click="emit('toggle-favorite', movie.id)"
        class="rounded-lg border border-slate-700 px-3 py-2"
        :class="movie.favorite ? 'text-pink-500' : 'text-white'"
        >
        {{ movie.favorite ? '♥' : '♡' }}
        </button>
      </div>
    </div>
  </article>
</template>