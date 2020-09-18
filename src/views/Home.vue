<template>
  <div id="world">
    <h1>Игра «Жизнь»</h1>
    <div>Нажмите «расставить клетки», а затем «старт» для запуска жизни.</div>
    <div>
      <button v-if="!squaresWithLife.length" @click="setRandomSquares">
        Расставить клетки
      </button>
      <div v-else>
        <button v-if="!started" @click="startLife">
          Запустить жизнь
        </button>
        <button v-else @click="stopLife">Сбросить жизнь</button>
      </div>
    </div>
    <canvas
      id="field"
      ref="field"
      :width="canvas.side"
      :height="canvas.side"
    ></canvas>
    <a href="https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life"
      ><small>Подробнее об игре в Вики</small></a
    >
  </div>
</template>

<script>
export default {
  name: "Home",
  data() {
    return {
      context: null,
      canvas: {
        side: 200
      },
      square: {
        color: "#ffffff",
        side: 10 //px
      },
      started: false,
      squaresWithLife: [], //положим сюда координаты живых квадратиков
      lifeCycle: 500, //ms
      interval: null
    };
  },
  computed: {
    xLength() {
      return this.canvas.side / this.square.side;
    },
    yLength() {
      return this.canvas.side / this.square.side;
    }
  },
  methods: {
    setRandomSquares() {
      // очищаем живые квадратики с предыдущего раза
      this.resetSquares();

      // герерируем живые квадратики
      for (let y = 0; y < this.yLength; y++) {
        for (let x = 0; x < this.xLength; x++) {
          this.makeLifeOrDeadSquare(x, y);
        }
      }

      // отрисовываем
      this.drawSquares(this.squaresWithLife);
    },
    makeLifeOrDeadSquare(x, y) {
      let isAlife = Math.random() >= 0.5;
      if (isAlife) {
        this.squaresWithLife.push({ x, y });
      }
    },
    drawSquares(squares) {
      for (const square of squares) {
        this.context.fillStyle = this.square.color;
        const SIDE = this.square.side;
        const { x, y } = square;
        this.context.fillRect(x * SIDE, y * SIDE, SIDE, SIDE);
      }
    },
    startLife() {
      console.log("Жизнь начинает жить");
      this.started = true;

      this.interval = setInterval(() => {
        // генерим новые квадратики по правилам
        let newLifeSquares = this.makeNewSquares();

        //очищаем поле
        this.resetSquares();

        //отрисовываем новые квадратики
        this.drawSquares(newLifeSquares);
        this.squaresWithLife = newLifeSquares;
      }, this.lifeCycle);
    },
    stopLife() {
      clearInterval(this.interval);
      this.started = false;
      this.resetSquares();
    },
    makeNewSquares() {
      // новые квадратики, которые пойдут в отрисовку
      let newSquares = [];

      for (let y = 0; y < this.yLength; y++) {
        for (let x = 0; x < this.xLength; x++) {
          const SQUARE = { x, y };
          // проверяем, есть ли в нашей коллекции
          const isExists = this.squaresWithLife.some(
            s => JSON.stringify(s) === JSON.stringify(SQUARE)
          );

          // число соседей
          const neighbors = this.countNeighbors(SQUARE);

          if (isExists) {
            if (neighbors === 2 || neighbors === 3) newSquares.push(SQUARE);
          } else {
            if (neighbors === 3) newSquares.push(SQUARE);
          }
        }
      }

      return newSquares;
    },
    countNeighbors(square) {
      let { x, y } = square;
      let neighborhoods = 0;
      const liveSquares = this.squaresWithLife;

      let n00 = function(x, y) {
        {
          return {
            x: x - 1,
            y: y - 1
          };
        }
      };
      let n01 = function(x, y) {
        {
          return {
            x,
            y: y - 1
          };
        }
      };
      let n02 = function(x, y) {
        {
          return {
            x: x + 1,
            y: y - 1
          };
        }
      };
      let n10 = function(x, y) {
        {
          return {
            x: x - 1,
            y
          };
        }
      };
      let n12 = function(x, y) {
        {
          return {
            x: x + 1,
            y
          };
        }
      };
      let n20 = function(x, y) {
        {
          return {
            x: x - 1,
            y: y + 1
          };
        }
      };
      let n21 = function(x, y) {
        {
          return {
            x,
            y: y + 1
          };
        }
      };
      let n22 = function(x, y) {
        {
          return {
            x: x + 1,
            y: y + 1
          };
        }
      };

      if (
        liveSquares.some(s => JSON.stringify(s) === JSON.stringify(n00(x, y)))
      )
        neighborhoods++;
      if (
        liveSquares.some(s => JSON.stringify(s) === JSON.stringify(n01(x, y)))
      )
        neighborhoods++;
      if (
        liveSquares.some(s => JSON.stringify(s) === JSON.stringify(n02(x, y)))
      )
        neighborhoods++;
      if (
        liveSquares.some(s => JSON.stringify(s) === JSON.stringify(n10(x, y)))
      )
        neighborhoods++;
      if (
        liveSquares.some(s => JSON.stringify(s) === JSON.stringify(n12(x, y)))
      )
        neighborhoods++;
      if (
        liveSquares.some(s => JSON.stringify(s) === JSON.stringify(n20(x, y)))
      )
        neighborhoods++;
      if (
        liveSquares.some(s => JSON.stringify(s) === JSON.stringify(n21(x, y)))
      )
        neighborhoods++;
      if (
        liveSquares.some(s => JSON.stringify(s) === JSON.stringify(n22(x, y)))
      )
        neighborhoods++;

      // возвращаем кол-во соседей
      return neighborhoods;
    },
    resetSquares() {
      this.squaresWithLife = [];
      this.context.clearRect(0, 0, this.canvas.side, this.canvas.side);
    }
  },
  mounted() {
    this.context = this.$refs["field"].getContext("2d");
  }
};
</script>

<style scoped>
#world {
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
}
#field {
  background-color: #757575;
  margin: 0 auto;
}
button {
  margin: 12px;
}
a {
  margin-top: 42px;
}
</style>
