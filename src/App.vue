<script>
import GameLoader from './components/GameLoader.vue';
import GameHistory from './components/History.vue';
import Scene1 from './components/Scene1.vue';

export default {
  name: 'App',
  components: {
    GameLoader,
    GameHistory,
    Scene1
  },
  data() {
    return {
      showLoader: true,
      showHistory: false,
      showScene1: false
    }
  },
  methods: {
    handleLoadingComplete() {
      this.showLoader = false;
      setTimeout(() => {
        this.showHistory = true;
      }, 300);
    },
    handleVideoComplete() {
      this.showHistory = false;
      setTimeout(() => {
        this.showScene1 = true;
      }, 300);
    }
  }
}
</script>

<template>
  <div id="app">
    <transition name="fade">
      <GameLoader v-if="showLoader" @loadingComplete="handleLoadingComplete" />
    </transition>

    <transition name="fade">
      <GameHistory v-if="showHistory" @videoComplete="handleVideoComplete" />
    </transition>

    <transition name="fade">
      <Scene1 v-if="showScene1" />
    </transition>
  </div>
</template>

<style>
html,
body,
#app {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
  background: black;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>