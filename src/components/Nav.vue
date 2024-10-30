<script setup>
import { useAuthStore } from '../stores/auth';
const authStore = useAuthStore();

</script>
<template> 
    <nav class="navbar navbar-expand-lg bg-warning">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">EVENTOS</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarSupportedContent">
      <ul class="navbar-nav me-auto mb-2 mb-lg-0" v-if="authStore.user">
        <li class="nav-item px-lg-5 h4">
          {{ authStore.user.name }}
        </li>
        <li class="nav-item px-lg-5">
          <RouterLink to="/cities" class="nav-link">Ciudades</RouterLink>
        </li>
        <li class="nav-item px-lg-5">
          <RouterLink to="/events" class="nav-link">Eventos</RouterLink>
        </li>
        <li class="nav-item px-lg-5">
          <RouterLink to="/graphic" class="nav-link">Gráfica</RouterLink>
        </li>
        <li class="nav-item px-lg-5">
          <RouterLink to="/reports" class="nav-link">Reportes</RouterLink>
        </li>
      </ul>
      <ul class="navbar-nav me-auto mb-2 mb-lb-0" v-if="authStore.user">
        <li class="nav-item px-lg-5">
          <button @click="authStore.logout" class="btn btn-warning">Salir</button>
        </li>
      </ul>
    </div>
  </div>
</nav>
</template>