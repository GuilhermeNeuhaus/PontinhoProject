<template>
  <div class="app-bg">
    <Jogadores ref="jogadores" />
  </div>
</template>

<script>
import Jogadores from './Jogadores.vue';

export default {
  name: 'App',
  components: {
    Jogadores
  }
}

</script>

<style scoped>
.app-bg {
  min-height: 100vh;
  background-image: url('@/assets/wallpaper.jpg');
  background-size: 100%; /* aumenta o tamanho da imagem */
  background-position: center;
  background-repeat: no-repeat;
  background-attachment: fixed; /* efeito de paralaxe */
}
</style>
