<script setup>
import { RouterLink } from 'vue-router'
</script>

<template>
  <main
    class="container vh-100 d-flex flex-column justify-content-center align-items-center text-center"
  >
    <div class="card p-5 shadow-lg" style="max-width: 600px; border-color: #4b2c85">
      <h1 class="card-title" style="color: #4b2c85">Erro 404</h1>
      <p class="card-text">Ops! A página não foi encontrada.</p>
      <RouterLink
        to="/"
        class="btn btn-primary mt-4"
        style="background-color: #22d87a; border-color: #22d87a"
        >Voltar</RouterLink
      >
    </div>
  </main>
</template>
