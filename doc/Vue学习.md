项目中采用了 `Vue + Ts`，同时使用`Vue`官方提供的`Api `来编写类组件以实现对`typescript`更好的支持。
### 组件的定义
- #### 类式组件
使用`vue-property-decorator`提供的`vue`类装饰器来编写

```js
// App.vue
<template>
  <div>
       <h3>类组件</h3>
   </div>
</template>
<script lang="ts" src="app.ts">
```

```js
// app.ts
// 引入vue装饰器
import {Component} from "vue-property-decorator";

// 用装饰器装饰类
@Component({})

// 导出类组件
export default class App extends Vue {
     mounted():void {
         console.log("class-component挂载完毕")
    }
}
```

- #### 扩展式组件
利用`Vue.extend()`扩展组件

```js
// ExtendComponent.vue

 import Vue from 'vue';
     export default Vue.extend({
        mounted() {
            console.log("extend-component挂载完毕")   
       }
 })
```

在`App`中注册

```js
import ExtendComponent from "./components/ExtendComponent.vue";
@Component({
      components:{ExtendComponent}
})
```

- #### 函数式组件
`template`标签中添加`functional`属性

```js
<template functional>
     <div>
             <h3>函数式组件</h3>
     </div>
</template>
```

函数式组件只有`template`标签，渲染速度最快，它可以接收上游传递下来的`props`，它没有`this`。
### 组件数据与通信
- 函数式组件
***函数式组件接收`prop`***

```html
<div>{{ prop.data }}</div>
```

***函数式触发事件, this.$parent***

```html
<button @click="parent.show(10)">按钮</button>
```

- 类组件
***类组件中定义`prop`***

```html
<Com p="传递值"></Com>
```

组件中：

```js
import { Prop } from 'vue-property-decorator'
@Prop({ default:'默认值'，type: string }) readonly p!: string;
```

***计算属性***

```js
get msg (): string {
 return this.$store.state.msg
}
```

***属性监测***

```js
@Watch("msg",{ deep:true ,immediate:true })
        onMsgChange (newValue:string,oldValue:string) :void {
            console.log("msg发生变化了","新值："+newValue,"旧值："+oldValue)
        }
```

***局部指令和局部过滤器***

```js
@Component({
            directives:{// 局部指令
                direc1:(el:HTMLElement,binding)=>console.log("direc1",binding.value)
            },
            filters:{// 局部过滤器
                filt1(data:string,arg:number=2):string{
                    return "宝马"+arg
                }
            }
        })
```

```html
<div>{{"bwm"|filt1}}</div>
<div v-direc1="'qqqqq'">direc1</div>
```

参考：https://www.cnblogs.com/wuqilang/p/12495477.html