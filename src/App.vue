<script setup lang="ts">
// import HelloWorld from './components/HelloWorld.vue'
// import TheWelcome from './components/TheWelcome.vue'
import CoordView from './components/CoordView.vue'
</script>

<template>
  <header>
    <h1>Rotation Converter</h1>
  </header>

  <main>
    <div class="coord_view">
      <CoordView/>
    </div>
    <!-- <RotMat /> -->
  </main>
</template>

<style scoped>
header {
  line-height: 2.5;
}

h1 {
  text-align: center;
}

.coord_view {
  width: 100%;
  text-align: center;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
