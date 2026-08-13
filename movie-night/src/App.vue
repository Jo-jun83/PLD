<script setup>
import { ref, onMounted, computed } from 'vue'
import AppHeader from './components/AppHeader.vue'
import AppFooter from './components/AppFooter.vue'
import MovieCard from './components/MovieCard.vue'
import MovieDetails from './components/MovieDetails.vue'

const movies = ref([])
const isLoading = ref(true)
const errorMessage = ref('')
const search = ref('')
const selectedCategory = ref('Tous')
const categories = [
  'Tous',
  'Action',
  'Comédie',
  'Science-fiction',
  'Animation'
]

const filteredMovies = computed(() => {
  return movies.value.filter((movie) => {
    const matchesSearch = movie.title
      .toLowerCase()
      .includes(search.value.toLowerCase())

    const matchesCategory =
      selectedCategory.value === 'Tous' ||
      movie.category === selectedCategory.value

    return matchesSearch && matchesCategory
  })
})

async function loadMovies() {
  isLoading.value = true
  errorMessage.value = ''

  try {
    await new Promise((resolve) => {
      setTimeout(resolve, 2000)
    })

    const response = await fetch(`${import.meta.env.BASE_URL}data/movies.json`)

    if (!response.ok) {
      throw new Error('Erreur lors du chargement des films')
    }

    const data = await response.json()
    movies.value = data
  } catch (loadError) {
    errorMessage.value = 'Impossible de charger les films.'
    console.error(loadError)
  } finally {
    isLoading.value = false
  }
}

onMounted(() => {
  loadMovies()
})

function resetFilters() {
  search.value = ''
  selectedCategory.value = 'Tous'
}

function toggleFavorite(movieId) {
  const movie = movies.value.find((movie) => movie.id === movieId)

  if (movie) {
    movie.favorite = !movie.favorite
  }
}

const favoriteCount = computed(() => {
  return movies.value.filter((movie) => movie.favorite).length
})

const currentView = ref('films')
const favoriteMovies = computed(() => {
  return movies.value.filter((movie) => movie.favorite)
})

const selectedMovie = ref(null)
</script>

<template>
  <div class="flex min-h-screen flex-col bg-slate-950">
    <AppHeader
      :favorite-count="favoriteCount"
      :current-view="currentView"
      @navigate="currentView = $event"
    />

    <main class="mx-auto w-full max-w-6xl flex-1 px-6 py-8">
  <h1 class="text-2xl font-bold text-white">
    {{ currentView === 'films'
      ? 'Que voulez-vous regarder ce soir ?'
      : 'Mes films favoris'
    }}
  </h1>

  <p class="mt-1 text-sm text-slate-400">
    {{ currentView === 'films'
      ? 'Parcourez, filtrez et gardez vos films préférés.'
      : 'Retrouvez ici les films que vous avez ajoutés à vos favoris.'
    }}
  </p>


  <div
    v-if="isLoading"
    class="flex min-h-[500px] flex-col items-center justify-center"
  >
    <div
      class="h-14 w-14 animate-spin rounded-full border-4 border-pink-900 border-t-pink-500"
    ></div>

    <p class="mt-4 text-base font-semibold text-white">
      Chargement des films...
    </p>

    <p class="mt-1 text-sm text-slate-500">
      Préparation de votre sélection
    </p>
  </div>

  <div
    v-else-if="errorMessage"
    class="flex min-h-[500px] items-center justify-center"
  >
    <div
      class="w-full max-w-md rounded-2xl border border-slate-700 bg-slate-900 px-8 py-12 text-center"
    >
      <h2 class="text-xl font-bold text-white">
        Impossible de charger les films
      </h2>

      <p class="mt-3 text-sm text-slate-400">
        Une erreur est survenue pendant le chargement.
      </p>

      <button
        @click="loadMovies"
        class="mt-8 w-full rounded-xl bg-pink-500 px-6 py-3 font-semibold text-white transition hover:bg-pink-400"
      >
        Réessayer
      </button>
    </div>
  </div>

  <div v-else class="mt-8">
  <!-- VUE FILMS -->
  <div v-if="currentView === 'films'">
    <input
      v-model="search"
      type="text"
      placeholder="Rechercher un film..."
      class="w-full rounded-xl border border-slate-700 bg-slate-900 px-4 py-3 text-white outline-none placeholder:text-slate-500 focus:border-pink-500"
    />

    <div class="mt-4 flex flex-wrap gap-2">
      <button
        v-for="category in categories"
        :key="category"
        @click="selectedCategory = category"
        class="rounded-lg px-4 py-2 text-sm font-semibold"
        :class="
          selectedCategory === category
            ? 'bg-pink-500 text-white'
            : 'bg-slate-800 text-slate-300 hover:bg-slate-700'
        "
      >
        {{ category }}
      </button>
    </div>

    <div
      v-if="filteredMovies.length === 0"
      class="mt-8 flex min-h-72 flex-col items-center justify-center rounded-xl border border-slate-800 bg-slate-900 p-8 text-center"
    >
      <h2 class="text-xl font-bold text-white">
        Aucun film trouvé
      </h2>

      <button
        @click="resetFilters"
        class="mt-6 rounded-xl bg-pink-500 px-5 py-3 font-semibold text-white"
      >
        Réinitialiser les filtres
      </button>
    </div>

    <div
      v-else
      class="mt-8 grid grid-cols-1 gap-6 sm:grid-cols-2 lg:grid-cols-4"
    >
      <MovieCard
        v-for="movie in filteredMovies"
        :key="movie.id"
        :movie="movie"
        @toggle-favorite="toggleFavorite"
        @show-details="selectedMovie = $event"
      />
    </div>
  </div>

  <!-- VUE FAVORIS -->
  <div v-else>
    <div
      v-if="favoriteMovies.length === 0"
      class="flex min-h-72 flex-col items-center justify-center text-center"
    >
      <h2 class="text-xl font-bold text-white">
        Aucun favori pour le moment
      </h2>

      <p class="mt-2 text-sm text-slate-400">
        Ajoutez des films à vos favoris pour les retrouver ici.
      </p>

      <button
        @click="currentView = 'films'"
        class="mt-6 rounded-xl bg-pink-500 px-5 py-3 font-semibold text-white"
      >
        Voir les films
      </button>
    </div>

    <div
      v-else
      class="grid grid-cols-1 gap-6 sm:grid-cols-2 lg:grid-cols-4"
    >
      <MovieCard
        v-for="movie in favoriteMovies"
        :key="movie.id"
        :movie="movie"
        @toggle-favorite="toggleFavorite"
        @show-details="selectedMovie = $event"
      />
    </div>
  </div>
</div>
  
</main>
    <MovieDetails
      v-if="selectedMovie"
      :movie="selectedMovie"
      @close="selectedMovie = null"
      @toggle-favorite="toggleFavorite"
    />
    <AppFooter />
  </div>
</template>
