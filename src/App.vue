<script setup>
import { RouterLink, RouterView } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'
</script>

<template>
  <header>
    <div class="wrapper">
      <nav>
        <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/about">About</RouterLink>
        <RouterLink :to="{name: 'ejercicio-1'}">ejercicio n° 1</RouterLink>
        <RouterLink :to="{name: 'ejercicio-2'}">ejercicio n° 2</RouterLink>
      </nav>
    </div>
  </header>

  <RouterView />
</template>
