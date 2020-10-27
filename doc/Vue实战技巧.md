# Vue 实战技巧

### vue 项目中的实战技巧

参考：

https://juejin.im/post/6844904191224184840

https://juejin.im/post/6844904196626448391

https://juejin.im/post/6844903632815521799

### 使用`$attr` 和 `$listener`

嵌套组件的传值 `$attr` 和 `$listener`

### 使用`require.context`实现前端工程自动化

`require.context`是一个**webpack**提供的**Api**,通过执行**require.context**函数获取一个特定的上下文,主要是用于实现自动化导入模块。
什么时候用？ 当一个 js 里面需要手动引入过多的其他文件夹里面的文件时，就可以使用。

```js
/**
 * @param {string} '../views' 查找文件路径
 * @param {boolean} 是否递归
 * @param {RegExp} 匹配以 .vue 结尾的文件
 */
const context = require.context("../views", false, /\.vue$/);
console.log(context.keys());
context.keys().forEach((file) => {
  // 组件路径
  console.log(file);
  // 组件
  console.log(context(file).default);
  // 执行相应的操作
});
```

### 使用.sync,更优雅的实现数据双向绑定

在`Vue`中，`props`属性是**单向数据**传输的,父级的`prop`的更新会向下流动到子组件中，但是反过来不行。可是有些情况，我们需要对`prop`进行“双向绑定”。上文中，我们提到了使用`v-model`实现双向绑定。但有时候我们希望一个组件可以实现多个数据的“双向绑定”，而`v-model`一个组件只能有一个(Vue3.0 可以有多个)，这时候就需要使用到`.sync`。

```js
 <CustomOverlay :visible.sync="visible"/>  或者
 <CustomOverlay v-model:visible="visible"/>
```

```vue
<template>
  <div
    class="custom-overlay"
    @click="handleClickOverlay"
    v-show="visible"
  ></div>
</template>
<script lang="ts">
export default {
  props: {
    visible: Boolean,
  },
  methods: {
    handleClickOverlay() {
      // 通过 update:key 修改 props
      this.$emit("update:visible", false);
    },
  },
};
</script>
<style lang="less" scoped>
.custom-overlay {
  width: 100vw;
  height: 100vw;
  background: rgba(100, 100, 100, 0.5);
  position: fixed;
  top: 0;
  left: 0;
}
</style>
```

### 动态组件，让页面渲染更灵活

```vue
<component :is="com" v-if="com"></component>
```

```js
import Acomponent from '../components/Acomponent.vue'
import Bcomponent from '../components/Bcomponent.vue'
import Ccomponent from '../components/Ccomponent.vue'

@Options({
  components: {
    CustomInput,
    CustomOverlay,
    Acomponent,
    Bcomponent,
    Ccomponent
  }
})
export default class About extends Vue {
  comMap = {
    A: Acomponent,
    B: Bcomponent,
    C: Ccomponent
  }

  com = this.comMap.B
}
</script>

```

### 组件内部监听生命周期函数

应用场景举例：我们经常会遇到全局在全局对象 `window` 监听事件，例如（`scroll`、`resize`），常规做法是在 `created` 监听事件，在 `beforeUnmount `取消事件，如果两短代码之间有很多业务代码会造成可读性较差（当然可以把两段代码放在一起）

```js
created () {
    window.addEventListener('resize', this.resize)
    // 业务代码 ...
 }

  // 业务代码 ...
  beforeUnmount () {
    window.removeEventListener('resize', this.resize)
    // 业务代码 ...
  }
```

使用 `hook `监听生命周期对象并消除 `window `上的事件

```js
window.addEventListener("resize", this.resize);
// $on 不会移除
// 监听一个自定义事件，但是只触发一次。一旦触发之后，监听器就会被移除。
this.$once("hook:beforeDestroy", () => {
  window.removeEventListener("resize", this.resize);
});
```

定时器清除：

```js
const timer = setInterval(() => {
  // 某些定时器操作
}, 500);
// 通过$once来监听定时器，在beforeDestroy钩子可以被清除。
this.$once("hook:beforeDestroy", () => {
  clearInterval(timer);
});
```

### 组件外部监听生命周期函数

应用场景举例：需要监听第三方组件事件（第三方组件未向外暴露事件）`@hook:钩子函数名`

```js
<component  @hook:updated="handleSelectUpdated"></component>
```

### 自定义指令的应用

https://cn.vuejs.org/v2/guide/custom-directive.html#ad
应用场景：

- 为组件添加 `loading` 效果

- 按钮级别权限控制 `v-permission`

- 代码埋点,根据操作类型定义指令

- `input`输入框自动获取焦点

### 深度 `watch` 与 `watch` 立即触发回调,我可以监听到你的一举一动

https://juejin.im/post/6844904196626448391#heading-17

### axios 封装

https://juejin.im/post/6844903652881072141

### 第三方 UI 组件样式的修改

在`vue`项目中样式都是写在<style lang="less" scoped></style>标签中的，加 scoped 是为了使得样式只在当前页面有效。这样编译后的`html`代码会带上 `data-v-xxx` 属性,`css`样式使用了属性选择器来防止同名样式污染：
![image](https://user-images.githubusercontent.com/63552419/95825045-dcf60980-0d62-11eb-9508-0b7ec47c5451.png)
然而当我们给第三方 UI 组件加上样式是不会编译出`data-v-xxx` 属性导致样式不生效，解决办法有多种，推荐使用深度选择器

```css
// less 或者 sass 中
.van-tabs /deep/ {
    color：blue;
}
// css 中
.van-tabs >>> {
    color：blue;
}
```

如果项目使用的是 `style module`，情况就有所不同。`style module`和`scoped`的区别在于，`style module`会在编译时改变类名但不会像`scoped`添加了属性导致作用域变化，`style module`在编写类名需要这样：`:class=$style[xxx]`
![image](https://user-images.githubusercontent.com/63552419/95826510-ff892200-0d64-11eb-90d6-5c57dd6dadce.png)
使用了`style module`之后如果给第三方组件添加样式编译后类名同样会被改变，这时候需要使用`global`来防止类名被更改从而使样式生效：

```css
:global（.xxx）{
   xxx
}
```

`module`与`scoped`的区别：
https://www.jianshu.com/p/b12941cf96b6

`css module`:
http://www.ruanyifeng.com/blog/2016/06/css_modules.html

### webpack-bundle-analyzer 查看打包文件体积

如果使用的是 VueCli 直接运行 `yarn build --report` 生成打包分析的报告文件，`report.html`，打开 `report.html` 查看

### 页面顶部加载进度条

https://juejin.im/post/6844903625370648589
