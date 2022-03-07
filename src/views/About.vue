<template>
  <div class="game">
    这是我的一个贪吃蛇小demo<br /><br />
    <canvas ref="panel" width="300" height="300"></canvas><br />
    <!-- <button @click="startGame">开始游戏</button> -->
    <el-row>
      <el-button v-show="!starting" type="primary" plain @click="startGame"
        >点击开始游戏</el-button
      >
    </el-row>
    <el-row>
      <el-button v-show="starting" type="primary" plain @click="pauseGame"
        >点击暂停游戏</el-button
      >
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
      // 游戏是否开始
      starting: false,
    };
  },
  methods: {
    startGame() {
      this.starting = true;
      // 每隔100ms重新绘制蛇
      this.timer = setInterval(() => {
        // 清空画布重新画蛇
        this.clearCanvas();
        this.drawFood(); //画一个食物
        this.moveSnake(); //里面如果蛇吃掉了食物，就会重新生成一个食物；在下一次进入定时器时候画到屏幕上
        this.drawSnake();
      }, 100);
    },

    // 暂停游戏
    pauseGame() {
      this.starting = false;
      clearInterval(this.timer);
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
        x: this.snake[0].x + this.speedX,
        y: this.snake[0].y + this.speedY,
      };
      this.snake.unshift(snakePart);
      // 判断是否吃到食物
      if (this.snake[0].x === this.food.x && this.snake[0].y === this.food.y) {
        // 重新生成一个食物
        this.addFood();
      } else {
        // 删除尾巴
        this.snake.pop();
      }
    },

    addFood() {
      // 如果食物的位置和蛇的身体重叠，那么重新生成一个食物
      this.food.x = this.randomTen(0, this.panel.width - 10);
      this.food.y = this.randomTen(0, this.panel.height - 10);
      console.log("食物坐标", this.food.x, this.food.y);
      // 返回数组中满足提供的测试函数的第一个元素的值。否则返回 undefined
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

    // 改变蛇的方向
    changeDirection(e) {
      // console.log("获取到了键盘事件", e.keyCode);
      // 如果按下了上且 speedY！=10（蛇不是往下走），那么就往上走（speedY=-10）
      // 判断用户按下了哪个键
      let press_LEFT = 37;
      let press_UP = 38;
      let press_RIGHT = 39;
      let press_DOWN = 40;
      // 判断蛇目前的走向
      let goingLeft = this.speedX === -10;
      let goingUp = this.speedY === -10;
      let goingRight = this.speedX === 10;
      let goingDown = this.speedY === 10;
      // 如果用户按下了 左，且蛇不是向右走
      if (e.keyCode === press_LEFT && !goingRight) {
        // 设置行走方向为左
        this.speedX = -10;
        this.speedY = 0;
      }
      // 如果用户按下了 上 且蛇不是向下走
      else if (e.keyCode === press_UP && !goingDown) {
        // 设置行走方向为上
        this.speedX = 0;
        this.speedY = -10;
      }
      // 入宫用户按下了 右 且蛇不是向左走
      else if (e.keyCode === press_RIGHT && !goingLeft) {
        this.speedX = 10;
        this.speedY = 0;
      }
      // 如果用户按下了 下 且蛇不是向上走
      else if (e.keyCode === press_DOWN && !goingUp) {
        this.speedX = 0;
        this.speedY = 10;
      }
    },
  },

  mounted() {
    // 监听键盘事件，获取用户当前按下的键。结合蛇的移动方向(speed),来对蛇进行移动
    document.addEventListener("keyup", this.changeDirection);
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
    // 生成一个食物;
    this.addFood();
  },
};
</script>

<style lang="css" scoped>
/* .game {
  width: 300;
  height: 300;
} */
</style>
