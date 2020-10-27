<template>
  <div id="snake-grid">
    <div class="overlay">
      <span v-if="!gameRunning">
        {{ gameOver ? `You reached a score of ${score}! Press Enter to restart` : 'Press a Arrow Key to start' }}
      </span>
      <span v-else-if="gameRunning">Score: {{ score }}</span>
    </div>
    <snakeSegment v-for="(segment, id) in snakeBody" :key="id" :x="segment.x" :y="segment.y" />
    <foodElement :x="food.x" :y="food.y" />
  </div>
</template>

<script>
  import snakeSegment from './SnakeSegment'
  import foodElement from './FoodElement'

  let lastRenderTime = 0
  let inputDirection = { x: 0, y: 0 }
  let beforeInputDirection = { x: 0, y: 0 }
  const gridSize = 21
  const growRate = 1
  let newSegments = 0

  export default {
    name: 'Grid',
    components: {
      snakeSegment,
      foodElement,
    },
    data() {
      return {
        request: '',
        gameRunning: false,
        snakeSpeed: 4,
        snakeBody: [{ x: 11, y: 11 }],
        food: {},
        score: 0,
        gameOver: false,
      }
    },
    created: function() {
      this.food = this.getRandomFoodPosition()
      window.addEventListener('keydown', this.onKey)
    },
    methods: {
      gameStart() {
        this.gameRunning = true
        this.gameLoop()
      },
      gameStop() {
        this.gameRunning = false
        cancelAnimationFrame(this.request)
      },
      gameReset() {
        this.score = 0
        this.snakeSpeed = 4
        this.snakeBody = [{ x: 11, y: 11 }]
        this.food = this.getRandomFoodPosition()
        this.gameOver = false
        lastRenderTime = 0
        inputDirection = { x: 0, y: 0 }
        beforeInputDirection = { x: 0, y: 0 }
      },
      gameLoop(currentTime) {
        if (this.gameOver) {
          return this.gameStop()
        }

        this.request = requestAnimationFrame(this.gameLoop)
        const secondsSinceLastRender = (currentTime - lastRenderTime) / 1000
        if (secondsSinceLastRender < 1 / this.snakeSpeed) return

        lastRenderTime = currentTime

        this.updateSnake()
        this.updateFood()
        this.checkDeath()
      },
      updateSnakeSpeed() {
        this.snakeSpeed = this.snakeSpeed + 0.25
      },
      updateSnake() {
        this.addSegments()

        beforeInputDirection = inputDirection
        const direction = inputDirection
        for (let i = this.snakeBody.length - 2; i >= 0; i--) {
          this.snakeBody[i + 1] = { ...this.snakeBody[i] }
        }

        this.snakeBody[0].x += direction.x
        this.snakeBody[0].y += direction.y
      },
      onSnake(position, { ignoreHead = false } = {}) {
        return this.snakeBody.some((segment, index) => {
          if (ignoreHead && index === 0) return false
          return this.equalPositions(segment, position)
        })
      },
      equalPositions(pos1, pos2) {
        return pos1.x === pos2.x && pos1.y === pos2.y
      },
      addSegments() {
        for (let i = 0; i < newSegments; i++) {
          this.snakeBody.push({ ...this.snakeBody[this.snakeBody.length - 1] })
        }

        newSegments = 0
      },
      updateFood() {
        if (this.onSnake(this.food)) {
          newSegments += growRate
          this.score += growRate * 5
          this.food = this.getRandomFoodPosition()
          this.updateSnakeSpeed()
        }
      },
      getRandomFoodPosition() {
        let newFoodPosition
        while (newFoodPosition == null || this.onSnake(newFoodPosition)) {
          newFoodPosition = this.randomGridPosition()
        }
        return newFoodPosition
      },
      randomGridPosition() {
        return {
          x: Math.floor(Math.random() * gridSize) + 1,
          y: Math.floor(Math.random() * gridSize) + 1,
        }
      },
      checkDeath() {
        this.gameOver = this.outsideGrid(this.snakeBody[0]) || this.snakeIntersection()
      },
      outsideGrid(position) {
        return position.x < 1 || position.x > gridSize || position.y < 1 || position.y > gridSize
      },
      snakeIntersection() {
        return this.onSnake(this.snakeBody[0], { ignoreHead: true })
      },
      onKey(event) {
        if (this.gameRunning) {
          switch (event.key) {
            case 'ArrowUp':
              if (beforeInputDirection.y !== 0) break
              inputDirection = { x: 0, y: -1 }
              break
            case 'ArrowDown':
              if (beforeInputDirection.y !== 0) break
              inputDirection = { x: 0, y: 1 }
              break
            case 'ArrowLeft':
              if (beforeInputDirection.x !== 0) break
              inputDirection = { x: -1, y: 0 }
              break
            case 'ArrowRight':
              if (beforeInputDirection.x !== 0) break
              inputDirection = { x: 1, y: 0 }
              break
          }
        } else if (this.gameOver && event.key === 'Enter') {
          this.gameReset()
        } else if ((!this.gameRunning && !this.gameOver && event.key === 'ArrowUp') || event.key === 'ArrowDown' || event.key === 'ArrowLeft' || event.key === 'ArrowRight') {
          this.gameStart()
          this.onKey(event)
        }
      },
    },
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  #snake-grid {
    background-color: #ccc;
    width: 100vmin;
    height: 100vmin;
    display: grid;
    grid-template-rows: repeat(21, 1fr);
    grid-template-columns: repeat(21, 1fr);
  }

  .overlay {
    background-color: rgb(100, 150, 200, 0.2);
    padding: 1em;
    position: absolute;
    color: white;
  }
</style>
