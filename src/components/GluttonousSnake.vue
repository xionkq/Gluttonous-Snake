<template>
  <div class="project" style="position: relative;width: 100%;height: 753px;background-color: #3493cb">
    <div class="mapDiv" style="position: absolute;top: 50%;left: 50%;transform: translate(-50%, -50%);width: 300px;height: 300px;background-color: #3ba1c4">

    </div>
  </div>
</template>

<script>
export default {
  name: 'GluttonousSnake',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      snakeBody: [[3, 1, 'head'], [2, 1, 'body'], [1, 1, 'body']],
      direct:'right',
      snakeMove:null,
      left: false,
      right: false,
      up: true,
      down: true,
      foodW: 20,
      foodH: 20,
      mapW: 300,
      mapH: 300,
      foodX: null,
      foodY: null,
    }
  },
  methods:{
    init(){
      for (var i = 0; i < this.snakeBody.length; i++) {
        var snake = document.createElement('div');
        snake.style.width = 20 + 'px';
        snake.style.height = 20 + 'px';
        snake.style.position = 'absolute';
        snake.style.left = this.snakeBody[i][0] * 20 + 'px';
        snake.style.top = this.snakeBody[i][1] * 20 + 'px';
        snake.classList.add(this.snakeBody[i][2]);
        document.getElementsByClassName('mapDiv')[0].appendChild(snake).classList.add('snake');
        //判断蛇头自身的方向
        switch (this.direct) {
          case 'right':
            break;
          case 'up':
            snake.style.transform = 'rotate(270deg)'
            break;
          case 'left':
            snake.style.transform = 'rotate(180deg)'
            break;
          case 'down':
            snake.style.transform = 'rotate(90deg)'
            break;
          default:
            break;
        }
      }

    },

    food() {
      this.foodX = Math.floor(Math.random() * (this.mapW / 20));
      this.foodY = Math.floor(Math.random() * (this.mapH / 20));
      for(let i = 0;i<this.snakeBody.length;i++){
        if(this.snakeBody[i][0] === this.foodX && this.snakeBody[i][1] === this.foodY){
          this.food()
        }
      }
      var food = document.createElement('div');
      food.style.width = this.foodW + 'px';
      food.style.height = this.foodH + 'px';
      food.style.position = 'absolute';
      food.style.left = this.foodX * 20 + 'px';
      food.style.top = this.foodY * 20 + 'px';
      document.getElementsByClassName('mapDiv')[0].appendChild(food).setAttribute('class', 'food')
    },

    move(){
      console.log(this.snakeBody.toString())
      for (var i = this.snakeBody.length - 1; i > 0; i--) {
        this.snakeBody[i][0] = this.snakeBody[i - 1][0];
        this.snakeBody[i][1] = this.snakeBody[i - 1][1];
      }
      // console.log(this.snakeBody.toString())
      // 假设已经运动，存放方向
      switch (this.direct) {
        case 'right':
          this.snakeBody[0][0] += 1;
          break;
        case 'up':
          this.snakeBody[0][1] -= 1;
          break;
        case 'left':
          this.snakeBody[0][0] -= 1;
          break;
        case 'down':
          this.snakeBody[0][1] += 1;
          break;
        default:
          break;
      }
      //判断是否撞墙
      if (this.snakeBody[0][0] < 0 || this.snakeBody[0][0] >= 300 / 20) {
        clearInterval(this.snakeMove)
        return
      }
      if (this.snakeBody[0][1] < 0 || this.snakeBody[0][1] >= 300 / 20) {
        clearInterval(this.snakeMove)
        return
      }
      //是否撞到自己
      var snakeHX = this.snakeBody[0][0];
      var snakeHY = this.snakeBody[0][1];
      for (var i = 1; i < this.snakeBody.length; i++) {
        if (snakeHX === this.snakeBody[i][0] && snakeHY === this.snakeBody[i][1]) {
          clearInterval(this.snakeMove)
          return
        }
      }
      if (this.snakeBody[0][0] === this.foodX && this.snakeBody[0][1] === this.foodY) {
        //吃到食物后自身加一
        var snakeEndX = this.snakeBody[this.snakeBody.length - 1][0];
        var snakeEndY = this.snakeBody[this.snakeBody.length - 1][1];
        switch (this.direct) {
          case 'right':
            this.snakeBody.push([snakeEndX + 1, snakeEndY, 'body']);
            break;
          case 'up':
            this.snakeBody.push([snakeEndX, snakeEndY - 1, 'body']);
            break;
          case 'left':
            this.snakeBody.push([snakeEndX - 1, snakeEndY, 'body']);
            break;
          case 'down':
            this.snakeBody.push([snakeEndX, snakeEndY + 1, 'body']);
            break;
          default:
            break;
        }
        //吃到食物
        this.removeClass('food');
        this.food();
      }
      this.removeClass('snake');
      this.init();


    },
    game(){
      this.snakeMove = setInterval(()=> {
        this.move()
      }, 200)
    },
    directChange(){
      document.onkeydown = (ev)=> {
        var ev = ev || window.event;
        var code = ev.keyCode;
        switch (code) {
          case 37:
            if (this.left) {
              this.direct = 'left';
              this.left = false;
              this.right = false;
              this.up = true;
              this.down = true;
            }
            break;
          case 38:
            if (this.up) {
              this.direct = 'up';
              this.left = true;
              this.right = true;
              this.up = false;
              this.down = false;
            }
            break;
          case 39:
            if (this.right) {
              this.direct = 'right';
              this.left = false;
              this.right = false;
              this.up = true;
              this.down = true;
            }
            break;
          case 40:
            if (this.down) {
              this.direct = 'down';
              this.left = true;
              this.right = true;
              this.up = false;
              this.down = false;
            }
            break;
          default:
            break;
        }
      }
    },
    removeClass(className) {
      var ele = document.getElementsByClassName(className);
      while (ele.length > 0) {
        ele[0].parentNode.removeChild(ele[0]);
      }
    },
  },
  mounted() {
    this.init()
    this.food()
    this.game()
    this.directChange()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
/deep/ .snake{
  background-color: #c8378c;
  border-radius: 10px;
}
/deep/ .food{
  background-color: wheat;
  border-radius: 10px;
}
</style>
