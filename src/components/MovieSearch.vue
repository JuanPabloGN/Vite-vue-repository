<script setup lang="ts">
import { ref } from 'vue'

const query = ref('');
const movies = ref<any[]>([]);
const loading = ref(false);
const error = ref('');

async function searchMovies() {
  if (!query.value) {
    movies.value = [];
    error.value = '';
    return;
  }
  loading.value = true;
  error.value = '';
  try {
    const apiKey = '59b56ad3';
    const response = await fetch(`https://www.omdbapi.com/?apikey=${apiKey}&s=${encodeURIComponent(query.value)}`);
    const data = await response.json();
    if (data.Response === 'True') {
      movies.value = data.Search;
      error.value = '';
    } else {
      movies.value = [];
      error.value = data.Error || 'No se encontraron resultados.';
    }
  } catch (err) {
    error.value = 'La pelicula que buscaste no existe o no se encuentra disponible.';
    movies.value = [];
  }
  loading.value = false;
}
</script>

<template>
  <div class="movie-search">
    <h2>Buscador de Películas</h2>
    <form @submit.prevent="searchMovies">
      <input
        v-model="query"
        type="text"
        placeholder="Escribe el nombre de la pelicula a buscar"
      />
      <button type="submit">Buscar</button>
    </form>
    <div v-if="loading">Buscando...</div>
    <div v-if="error" class="error">{{ error }}</div>
    <div v-if="movies.length">
      <ul class="movie-list">
        <li v-for="movie in movies" :key="movie.imdbID">
          <img :src="movie.Poster !== 'N/A' ? movie.Poster : 'https://via.placeholder.com/100x150?text=No+Image'" alt="Poster" width="100" height="150" />
          <div>
            <strong>{{ movie.Title }}</strong>
            <p>Año: {{ movie.Year }}</p>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
.movie-search {
  max-width: 420px;
  margin: 2rem auto;
  padding: 2rem;
  background: #000000;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(122, 120, 120, 0.1);
}
form {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
}
input[type="text"] {
  flex: 1;
  padding: 0.5rem;
  border: 1px solid #858585;
  border-radius: 4px;
}
button {
  padding: 0.5rem 1rem;
  background: #42b883;
  color: #000000;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
button:hover {
  background: #36896c;
}
.movie-list {
  list-style: none;
  padding: 0;
  
}
.movie-list li {
  display: list-item;
  align-items: center;
  gap: 1rem;
  margin-bottom: 1rem;
  border-bottom: 1px solid #eee;
  padding-bottom: 1rem;
}
.error {
  color: #e74c3c;
  margin-bottom: 1rem;
}
</style>
