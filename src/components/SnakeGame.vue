<template>
  <div class="game-container">
    <div class="scoreboard">
      <div class="current-score">Current Score: <span class="badge bg-primary">{{ currentScore }}</span></div>
      <div class="high-score">High Score: <span class="badge bg-success">{{ highScore }}</span></div>
    </div>
    <canvas ref="gameCanvas" width="400" height="400"></canvas>
  </div>
</template>

<script>
export default {
  name: 'SnakeGame',
  data() {
    return {
      context: null,
      snake: [{ x: 10, y: 10 }],
      direction: 'RIGHT',
      food: { x: 5, y: 5 },
      gridSize: 20,
      interval: null,
      isRunning: false,
      currentScore: 0,
      highScore: 0
    };
  },
  mounted() {
    this.context = this.$refs.gameCanvas.getContext('2d');
    document.addEventListener('keydown', this.changeDirection);
    document.addEventListener('keydown', this.preventArrowScroll);
    this.loadHighScore();
  },
  methods: {
    start() {
      if (!this.isRunning) {
        this.resetGame();
        this.interval = setInterval(this.gameLoop, 100);
        this.isRunning = true;
      }
    },
    stop() {
      if (this.isRunning) {
        clearInterval(this.interval);
        this.isRunning = false;
      }
    },
    preventArrowScroll(event) {
      if (['ArrowUp', 'ArrowDown', 'ArrowLeft', 'ArrowRight'].includes(event.key)) {
        event.preventDefault();
      }
    },
    changeDirection(event) {
      const key = event.keyCode;
      if (key === 37 && this.direction !== 'RIGHT') {
        this.direction = 'LEFT';
      } else if (key === 38 && this.direction !== 'DOWN') {
        this.direction = 'UP';
      } else if (key === 39 && this.direction !== 'LEFT') {
        this.direction = 'RIGHT';
      } else if (key === 40 && this.direction !== 'UP') {
        this.direction = 'DOWN';
      }
    },
    gameLoop() {
      this.updateSnakePosition();
      if (this.checkCollision()) {
        this.stop();
        this.handleGameOver();
      } else {
        this.drawGame();
      }
    },
    updateSnakePosition() {
      let head = { ...this.snake[0] };
      if (this.direction === 'RIGHT') head.x++;
      else if (this.direction === 'LEFT') head.x--;
      else if (this.direction === 'UP') head.y--;
      else if (this.direction === 'DOWN') head.y++;
      this.snake.unshift(head);
      if (head.x === this.food.x && head.y === this.food.y) {
        this.generateFood();
        this.currentScore++;
        if (this.currentScore > this.highScore) {
          this.highScore = this.currentScore;
          this.saveHighScore();
        }
      } else {
        this.snake.pop();
      }
    },
    generateFood() {
      this.food = {
        x: Math.floor(Math.random() * (this.$refs.gameCanvas.width / this.gridSize)),
        y: Math.floor(Math.random() * (this.$refs.gameCanvas.height / this.gridSize))
      };
    },
    checkCollision() {
      const head = this.snake[0];
      if (head.x < 0 || head.x >= this.$refs.gameCanvas.width / this.gridSize ||
        head.y < 0 || head.y >= this.$refs.gameCanvas.height / this.gridSize) {
        return true;
      }
      for (let i = 1; i < this.snake.length; i++) {
        if (head.x === this.snake[i].x && head.y === this.snake[i].y) {
          return true;
        }
      }
      return false;
    },
    drawGame() {
      // Draw background
      this.context.fillStyle = '#8FBC8F';
      this.context.fillRect(0, 0, this.$refs.gameCanvas.width, this.$refs.gameCanvas.height);

      // Draw snake
      this.snake.forEach(part => {
        if (part === this.snake[0]) {
          this.context.fillRect(part.x * this.gridSize, part.y * this.gridSize, this.gridSize, this.gridSize);
          this.context.font = '20px Bootstrap Icons';
          this.context.fillText('\u{1F438}', part.x * this.gridSize, (part.y + 1) * this.gridSize);
        } else {
          this.context.fillRect(part.x * this.gridSize, part.y * this.gridSize, this.gridSize, this.gridSize);
          this.context.font = '20px Bootstrap Icons';
          this.context.fillText('\u{1F4D7}	', part.x * this.gridSize, (part.y + 1) * this.gridSize);
        }
        
      });

      // Draw food
      this.context.font = '20px Bootstrap Icons';
      this.context.fillStyle = 'red';
      this.context.fillText('\u{1F34E}', this.food.x * this.gridSize, (this.food.y + 1) * this.gridSize);
    },
    resetGame() {
      this.snake = [{ x: 10, y: 10 }];
      this.direction = 'RIGHT';
      this.food = { x: 5, y: 5 };
      this.currentScore = 0;
    },
    handleGameOver() {
      alert('Game Over');
    },
    saveHighScore() {
      localStorage.setItem('highScore', this.highScore);
    },
    loadHighScore() {
      const savedHighScore = localStorage.getItem('highScore');
      if (savedHighScore) {
        this.highScore = parseInt(savedHighScore);
      }
    }
  }
};
</script>

<style scoped>
.game-container {
  display: flex;
  justify-content: center;
  align-items: center;
}
canvas {
  border: 1px solid #000;
  background-color: #8FBC8F; /* green color for grass */
}
.scoreboard {
  position: absolute;
  top: 10px;
  right: 10px;
  font-size: 18px;
}
.current-score,
.high-score {
  margin-bottom: 5px;
}
</style>