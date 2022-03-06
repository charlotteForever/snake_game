<template>
  <div class="game">
    这是我的一个贪吃蛇小demo<br />
    <canvas ref="panel" width="300" height="300"></canvas><br />
    <!-- <button @click="startGame">开始游戏</button> -->
    <el-row>
      <el-button type="primary" plain @click="startGame">主要按钮</el-button>
    </el-row>
  </div>
</template>

<script>
export default {
  name: "snake_name",
  data() {
    return {
      // 蛇的身体
      snake: [
        { x: 150, y: 150 },
        { x: 140, y: 150 },
        { x: 130, y: 150 },
        { x: 120, y: 150 },
      ],
      speedX: 10,
      speedY: 0,
      context: "",
      penel: {},
      // 食物的位置
      food: {
        x: 0,
        y: 0,
      },
      // 存放定时器
      timer: {},
    };
  },
  methods: {
    startGame() {
      // 每隔100ms重新绘制蛇
      this.timer = setInterval(() => {
        // 清空画布重新画蛇
        this.clearCanvas();
        // 先画蛇
        this.drawSnake();
        this.drawFood(); //画一个食物
        this.moveSnake(); //里面如果蛇吃掉了食物，就会重新生成一个食物；在下一次进入定时器时候画到屏幕上
      }, 100);
    },

    //清空画布
    clearCanvas() {
      this.context.fillStyle = "white";
      this.context.strokeStyle = "black";
      this.context.fillRect(0, 0, this.panel.width, this.panel.height);
      this.context.strokeRect(0, 0, this.panel.width, this.panel.height);
    },

    // 画蛇身体的某一块
    drawSnakePart(snakePart) {
      // 确定画笔的样式
      this.context.fillStyle = "green";
      this.context.strokeStyle = "lightgreen";
      // 画身体主要框架
      this.context.fillRect(snakePart.x, snakePart.y, 10, 10);
      // 画身体的边框
      this.context.strokeRect(snakePart.x, snakePart.y, 10, 10);
    },

    // 画整条蛇
    drawSnake() {
      this.snake.forEach(this.drawSnakePart);
    },

    // 让蛇动起来
    moveSnake() {
      // 新建一个头部
      let snakePart = {
        x: this.snake.x + this.speedX,
        y: this.snake.y + this.speedY,
      };
      this.snake.unshift(snakePart);
      // 判断是否吃到食物
      if (this.snake[0].x === this.food.x && this.snake[0].y === this.food.y) {
        // 重新生成一个食物
        addFood();
      } else {
        // 删除尾巴
        this.snake.pop();
      }
    },

    addFood() {
      // 如果食物的位置和蛇的身体重叠，那么重新生成一个食物
      this.food.x = this.randomTen(0, this.panel.width - 10);
      this.food.y = this.randomTen(0, this.panel.height - 10);
      let overlapped = this.snake.find(
        (el) => el.x === this.food.x && el.y === this.food.y
      );
      console.log("overlapped", overlapped);
      if (overlapped) {
        // 再生成一次食物
        this.addFood();
      }
    },

    // 画出生成的食物
    drawFood() {
      this.context.fillStyle = "red";
      this.context.strokeStyle = "black";
      this.context.fillRect(this.food.x, this.food.y, 10, 10);
      this.context.strokeRect(this.food.x, this.food.y, 10, 10);
    },

    // 随机数生成10的倍数
    randomTen(min, max) {
      return Math.floor((Math.random() * (max - min + 1) + min) / 10) * 10;
    },
  },

  mounted() {
    // 先创建面板
    /** @type {HTMLCanvasElement} */
    // panel在这里初始化
    this.panel = this.$refs.panel;
    this.context = this.panel.getContext("2d");
    this.context.fillStyle = "white";
    this.context.strokeStyle = "black";
    // 控制画布的横向偏移、纵向偏移、画布长度、宽度
    this.context.fillRect(0, 0, this.panel.width, this.panel.height);
    // 绘制画布的边框
    this.context.strokeRect(0, 0, this.panel.width, this.panel.height);
    // 画整条蛇
    // this.drawSnake();
    console.log("mounted1", this.food.x, this.food.y);
    // 生成一个食物
    this.addFood();
    console.log("mounted2", this.food.x, this.food.y);
  },
};
</script>

<style lang="css" scoped>
/* .game {
  width: 300;
  height: 300;
} */
</style>
