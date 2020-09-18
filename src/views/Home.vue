<template>
  <div id="world">
    <h1>Игра «Жизнь»</h1>
    <div>Кликнете в любом месте зелёного поля для создания жизни.</div>
    <div>Нажмите старт для запуска жизни.</div>
    <div>
      <button @click="setRandomSquares">Расставить клетки</button>
      <button @click="startLife">Старт</button>
    </div>
    <canvas
      id="field"
      ref="field"
      @click="makeLifeSquares()"
      width="600"
      height="600"
    ></canvas>
  </div>
</template>

<script>
export default {
  name: "Home",
  data() {
    return {
      context: null,
      canvas: {
        side: 600
      },
      square: {
        color: "#eec842",
        side: 10 //px
      },
      started: false,
      squaresWithLife: [], //положим сюда координаты живых квадратиков
      lifeCycle: 240 //ms
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
          this.makeLifeSquares(x, y);
        }
      }

      // отрисовываем
      this.drawSquares(this.squaresWithLife);
    },
    makeLifeSquares(x, y) {
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
      setInterval(() => {
        // генерим новые квадратики по правилам
        let newLifeSquares = this.makeNewSquares();

        //очищаем поле
        //todo

        //отрисовываем новые квадратики
        this.drawSquares(newLifeSquares);
      }, this.lifeCycle);
    },
    makeNewSquares() {
      // сюда сложим новорожденных
      let birnSquares = [];
      // продолжают жить
      let stillAlive = [];
      // умершие
      let deadSquares = [];

      // герерируем живые квадратики
      for (let y = 0; y < this.yLength; y++) {
        for (let x = 0; x < this.xLength; x++) {
          const SQUARE = { x, y };
          // проверяем, есть ли в нашей коллекции
          const isExists = this.squaresWithLife.some(
            s => JSON.stringify(s) === JSON.stringify(SQUARE)
          );
          if (isExists) {
            //todo
          }
        }
      }
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
}
#field {
  background-color: darkseagreen;
  margin: 0 auto;
}
button {
  margin: 12px;
}
</style>
