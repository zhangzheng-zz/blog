# html 换行产生空白符的坑及解决办法

```HTNL
 <span>{{showInTheGameData.actDate}}</span>
 (<span>{{showInTheGameData.periodNum}}</span>)期
 <span>参赛中</span>
// 第二个 span 标签的左右会产生空白符
```

**解决方法：**

- 1、万能 flex 布局（推荐）
- 2、中间 span 标签添加 margin-left:-3px 消除，缺点是空白符作为文本节点大小受父节点影响，-3px 不好确定
- 3、直接把 span 标签写成一行
- 4、终极解决方案，给 span 标签父节点添加 font-size：0 属性消除空白文本，再在 span 标签里面设置字体大小（推荐）
- 5、扩展：新属性：white-space-collapsing（目前还未兼容）
