<script setup>
import { ref, computed } from "vue";

import BlogPost from './components/BlogPost.vue';
import LoadingSpinner from './components/LoadingSpinner.vue';
import PaginatePost from './components/PaginatePost.vue';

const posts = ref([]);
const postXpage = 10;
const inicio = ref(0);
const fin = ref(postXpage);
const loading = ref(true);

const favorito = ref('')

const cambiarFavorito = (title) => {
  favorito.value = title;
}

const next = () => {
  inicio.value = inicio.value + postXpage
  fin.value = fin.value + postXpage
}

const prev = () => {
  inicio.value += -postXpage;
  fin.value += -postXpage;
}

fetch('https://jsonplaceholder.typicode.com/posts')
  .then((res) => res.json())
  .then((data) => {
      posts.value = data;
  })
  .catch((e) => console.log(e))
  .finally(() => (loading.value = false));

  //También se puede calcular el tamaño de los post con una función computada
  //const maxLength = computed(() => posts.value.length);
  //Se reemplaza en el PaginatePost con esto:
  //:maxLength="maxLength"
</script>

<template>
  <LoadingSpinner v-if="loading" />
  <div class="container" v-else>
    <h1>APP</h1>
    <h2>Mi Post Favorito: {{ favorito }}</h2>

    <PaginatePost 
      @prev="prev" 
      @next="next"
      :inicio="inicio"
      :fin="fin"
      :maxLength="posts.length"
      class="mb-2"
    />

    <BlogPost
      v-for="post in posts.slice(inicio, fin)"
      :key="post.id"
      :title="post.title" 
      :id="post.id" 
      :body="post.body"
      @cambiarFavoritoNombre="cambiarFavorito"
      class="mb-2"
    ></BlogPost>
  </div>
</template>