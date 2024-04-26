<script setup>
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <header>
    <nav class="navbar navbar-expand navbar-light bg-light">
      <ul class="nav navbar-nav">
        <li class="nav-item">
          <RouterLink to="/" class="nav-link">Inicio</RouterLink>
        </li>
        <li class="nav-item">
          <RouterLink :to="{name: 'ejercicio-1'}" class="nav-link" >Ejercicio n° 1</RouterLink>
        </li>
        <li class="nav-item">
          <RouterLink :to="{name: 'ejercicio-2'}" class="nav-link">Ejercicio n° 2</RouterLink>
        </li>
      </ul>
    </nav>
  </header>
  <div class="col-lg mx-auto p-2 py-md-2">
  <main>
    <RouterView />
  </main>
</div>
</template>
