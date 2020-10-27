# CSS3 中的 linear-gradient 在 picker/area 组件中的应用

## CSS3 中的 linear-gradient 在 picker/area 组件中的应用？

[linear-gradient()](https://developer.mozilla.org/zh-CN/docs/Web/CSS/linear-gradient) **linear-gradient() 函数用于创建一个表示两种或多种颜色线性渐变的图片。**

下面来看一下 `linear-gradient` 在 `vant `的 [area](https://youzan.github.io/vant/#/zh-CN/area) 组件里面的一个应用。最近遇到一个小需求需要适配一下 `vant `的 `area `组件的暗色模式（把背景改成黑色字体改成白色就行），唯一的问题就是**透明渐变**的效果是怎么实现的？

**area 组件：**

![Snipaste_2020-10-21_18-54-58](https://user-images.githubusercontent.com/63552419/96710717-051ee180-13cf-11eb-9d8e-fe10b03b6597.png)

**先把背景改成黑色字体改成白色马上就明白它的实现原理：**

![Snipaste_2020-10-21_19-00-12](https://user-images.githubusercontent.com/63552419/96711209-ca697900-13cf-11eb-9360-035c3f956318.png)

观察一下代码：一个 `mask `**白色的蒙版**，**上下垂直角度**各有一个**透明度的渐变效果**，**越靠近中间透明度越高**。

![Snipaste_2020-10-21_19-04-31](https://user-images.githubusercontent.com/63552419/96711535-4d8acf00-13d0-11eb-878a-337795fec8d2.png)

```css
background-image: linear-gradient(
    180deg,
    hsla(0, 0%, 100%, 0.9),
    hsla(0, 0%, 100%, 0.4)
  ), linear-gradient(0deg, hsla(0, 0%, 100%, 0.9), hsla(0, 0%, 100%, 0.4));
```

改成**暗色模式**：`mask `蒙版改成**黑色**。

```css
background-image: linear-gradient(
    180deg,
    hsla(0, 0%, 0%, 0.9),
    hsla(0, 0%, 0%, 0.4)
  ), linear-gradient(0deg, hsla(0, 0%, 0%, 0.9), hsla(0, 0%, 0%, 0.4));
```

**最终效果：**

![Snipaste_2020-10-21_19-09-22](https://user-images.githubusercontent.com/63552419/96711979-f9341f00-13d0-11eb-82e2-f2edf4889423.png)

黑暗模式，CSS 的控制可以使用 CSS 变量。
