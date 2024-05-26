<template>
  <div id="app" class="text-center">
    <h1 class="mb-2">Snake Game</h1>
    <SnakeGame ref="snakeGame" class="mb-2" />
    <div>
      <button class="btn btn-success mr-2" @click="startGame">Start</button>
      <button class="btn btn-danger" @click="stopGame">Stop</button>
    </div>
  </div>
</template>

<script>
import SnakeGame from './components/SnakeGame.vue';

export default {
  name: 'App',
  components: {
    SnakeGame
  },
  methods: {
    startGame() {
      this.$refs.snakeGame.start();
    },
    stopGame() {
      this.$refs.snakeGame.stop();
    }
  }
};
</script>

<style scoped>
h1 {
  margin-top: 20px;
}
</style>
