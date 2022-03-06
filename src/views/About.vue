<template>
  <div class="game">
    这是我的一个贪吃蛇小demo<br />
    <canvas ref="panel" width="300" height="300"></canvas>
  </div>
</template>

<script>
export default {
  name: "snake_name",
  data() {
    return {
      context: "",
    };
  },
  mounted() {
    // 先创建面板
    /** @type {HTMLCanvasElement} */
    this.context = this.$refs.panel.getContext("2d");
    this.context.fillStyle = "white";
    this.context.strokeStyle = "black";
    // 控制画布的横向偏移、纵向偏移、画布长度、宽度
    this.context.fillRect(
      0,
      0,
      this.$refs.panel.width,
      this.$refs.panel.height
    );
    // 绘制画布的边框
    this.context.strokeRect(
      0,
      0,
      this.$refs.panel.width,
      this.$refs.panel.height
    );
  },
};
</script>

<style lang="css" scoped>
/* .game {
  width: 300;
  height: 300;
} */
</style>
