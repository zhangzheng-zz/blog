# rem 移动端适配

**_rem_**
**_vw_**
**_媒体查询_**

- 设计稿提供的宽度为 `1080px`
- `font-size` 自定义为 `100px` 方便计算

```
e.g. 假设设计稿上面一个 `DOM` 的宽度为 `320px`，那么在编写样式的时候直接除以 `100`, 单位改为 `rem`，即`width: 3.2rem`
```

- 由于手机的宽度是不一致的，不会满足设计稿上面的 1080px, 所以 font-size 不能写死为 100px
- 如果手机能够适配 vw 单位，使用 vw 单位非常合适，则 font-size 需要满足以下公式：

```
设备宽度 / font-size  的比例保持一致
1080px / 100px = 100vw / font-size
```

- 由上公式计算可得 `font-size` 的大小：`10000 / 1080 = 9.259259259...` 单位是 `vw`
- 对于某些手机不适配` vw` 单位的情况，使用媒体查询动态改变 `font-size` 的大小 (保持设备与 `font-size` 比例和 `1080 / 100` 一致)
- 项目中，`css`全局的` html`的 `font-size` 可以这样写：

```css
html {
  font-size: 9.25925925vw
}

@media screen and (max-width: 320px) {
  html {
      font-size:29.629px;
      font-size: 9.25925925vw
  }
}

@media screen and (min-width: 321px) and (max-width:360px) {
  html {
      font-size:33.333px;
      font-size: 9.25925925vw
  }
}
......
```
