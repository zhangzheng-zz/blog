# Vue 动画实战

### vue 动画钩子实践

参考：https://juejin.im/post/6869195042599206919

Vue 动画钩子：https://cn.vuejs.org/v2/guide/transitions.html#JavaScript-%E9%92%A9%E5%AD%90

1、跟进列表

![image](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e2bed0297ec14dd68d6a5653796df45c~tplv-k3u1fbpfcp-zoom-1.image)

2、段落列表

![image](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4910838a46cd40f7821a7f63ec7efd25~tplv-k3u1fbpfcp-zoom-1.image)

3、交错列表

![image](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0cc5d2aac2e549868dd67fd73bcfdf9f~tplv-k3u1fbpfcp-zoom-1.image)

主要实现代码：

```js
  methods: {
    beforeEnter (el) {
      // console.log(el)
      // el.style.opacity = 0
      el.style.transform = 'translateX(100%)'
      el.style.height = '0'
    },
    enter (el, done) {
      // dataset 元素上自定义属性
      // console.log(el.dataset)
      const delay = el.dataset.index * 100
      this.timer.push(
        setTimeout(() => {
          /**
           * 动画
           */
          // 1、跟进列表
          //   el.style.transition = 'opacity 0.4s ease-in'
          //   el.style.opacity = 1

          // 2、段落列表
          //   el.style.transition = 'transform 0.4s ease-in-out'
          //   el.style.transform = 'translateX(0)'

          // 3、交错列表 只实现右上动效
          el.style.transition = 'all .4s ease-in'
          el.style.height = '100px'
          el.style.transform = 'translateX(0)'
          done()
        }, delay)
      )
    }
  },
```

源码：

```vue
<template>
  <div class="pic"></div>
  <div class="flex">
    <div class="card"></div>
    <div class="card"></div>
    <div class="card"></div>
    <div class="card"></div>
  </div>
  <transition-group
    name="more"
    v-bind:css="false"
    v-on:before-enter="beforeEnter"
    v-on:enter="enter"
  >
    <div
      class="item"
      v-if="show"
      v-for="(item, index) in arr"
      v-bind:data-index="index"
      v-bind:key="item"
    >
      <div class="square"></div>
      <div class="content">
        <div>&nbsp;</div>
        <div>&nbsp;</div>
      </div>
    </div>
  </transition-group>
</template>
<script>
export default {
  data() {
    return {
      show: false,
      arr: [1, 2, 3, 4, 5, 6, 7, 8],
      timer: [],
    };
  },
  methods: {
    beforeEnter(el) {
      // console.log(el)
      // el.style.opacity = 0
      el.style.transform = "translateX(100%)";
      el.style.height = "0";
    },
    enter(el, done) {
      // dataset 元素上自定义属性
      // console.log(el.dataset)
      const delay = el.dataset.index * 100;
      this.timer.push(
        setTimeout(() => {
          /**
           * 动画
           */
          // 1、跟进列表
          //   el.style.transition = 'opacity 0.4s ease-in'
          //   el.style.opacity = 1

          // 2、段落列表
          //   el.style.transition = 'transform 0.4s ease-in-out'
          //   el.style.transform = 'translateX(0)'

          // 3、交错列表
          el.style.transition = "all .4s ease-in";
          el.style.height = "100px";
          el.style.transform = "translateX(0)";
          done();
        }, delay)
      );
    },
  },
  mounted() {
    setTimeout(() => {
      this.show = !this.show;
    }, 500);
  },
  // vue2.x  this.$once('hook:beforeDestroy', () => {  })
  beforeUnmount() {
    clearTimeout(this.timer.pop());
  },
};
</script>
<style lang="less" scoped>
.pic {
  width: 97%;
  height: 120px;
  margin: 20px 5px;
  border-radius: 8px;
  background: #cae5e8;
}
.flex {
  display: flex;
  justify-content: space-between;
  .card {
    width: 65px;
    height: 65px;
    margin: 15px 12px;
    margin-top: 0px;
    border-radius: 8px;
    background: #cae5e8;
  }
}
.item {
  margin: 5px;
  padding: 0px;
  overflow: hidden;
}
.item div {
  display: inline;
  float: left;
}
// 清除浮动
.item::after {
  content: "";
  clear: both;
  display: block;
}
.square {
  width: 20%;
  height: 75px;
  background: #cae5e8;
  border-radius: 8px;
}
.content {
  width: 79%;
}
.content div {
  margin: 15px;
  margin-top: 0px;
  padding: 0px;
  width: 95%;
  border-radius: 8px;
  line-height: 30px;
  background: #99d1d3;
}
.content div:last-child {
  width: 65%;
  background: #cae5e8;
}
</style>
```
