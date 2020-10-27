# 第二部分 this 和对象原型

### 第 1 章 关于 this

this 实际上是在函数**被调用时**发生的绑定，它指向什么完全取决于函数在哪里**被调用**。

### 第 2 章 this 全面解析

**绑定规则：** 找到**调用位置**，然后判断需要应用下面四条规则中的哪一条

**一、默认绑定**：无法应用其他规则时的**默认规则**

- **非严格模式下**：foo() **不带任何修饰**的函数引用进行调用的，this 指向全局 **window 对象**

```js
function foo() {
  console.log(this.a);
}
var a = 2;
foo(); // 2
```

- **严格模式下**：this 严格模式下与 foo() 的调用位置无关：

```js
function foo() {
  "use strict";
  console.log(this.a);
}
var a = 2;
foo(); // TypeError: this is undefined
```

```js
function foo() {
  console.log(this.a);
}
var a = 2;
(function () {
  "use strict";
  foo(); // 2
})();
```

**二、隐式绑定**：调用位置是否有**上下文对象**，或者说是否被某个对象拥有或者包含 foo() 作为 obj 的属性被调用

```js
function foo() {
  console.log(this.a);
}
var obj = {
  a: 2,
  foo: foo,
};
obj.foo(); // 2
```

对象属性引用链中只有**最顶层**或者说**最后一层**会影响调用位置：

```js
function foo() {
  console.log(this.a);
}
const obj2 = {
  a: 42,
  foo: foo,
};
const obj1 = {
  a: 2,
  obj2: obj2,
};
obj1.obj2.foo(); // 42
```

**隐式丢失的现象：**

```js
function foo() {
  console.log(this.a);
}
const obj = {
  a: 2,
  foo: foo,
};
const bar = obj.foo; // 函数别名！
const a = "oops, global"; // a 是全局对象的属性
// bar 实际引用了 foo 相当于 foo()
bar(); // "oops, global"
```

发生在回调函数中：

```js
function foo() {
  console.log(this.a);
}
function doFoo(fn) {
  // fn 其实引用的是foo
  fn(); // <-- 调用位置！
}
const obj = {
  a: 2,
  foo: foo,
};
const a = "oops, global"; // a 是全局对象的属性
doFoo(obj.foo); // "oops, global"
```

**三、显式绑定**：**call、apply、bind**

**自定义 bind 函数**:

```js
function foo(something) {
  console.log(this.a, something);
  return this.a + something;
}

var obj = {
  a: 2,
};

// 自定义 bind 绑定函数
function bind(fn, obj) {
  return function () {
    return fn.apply(obj, arguments);
  };
}

var bar = bind(foo, obj);
bar(3); // 5
```

ES5 中提供了内置的方法 Function.prototype.bind，可以直接使用：`foo.bind(obj，2)()`

**四、new 绑定**

new 执行了如下步骤：

- 新建一个对象
- 新建对象的原型（`obj.__proto__`）执行构造函数的原型（`foo.prototype`）
- this 绑定到新对象
- 返回这个新对象

```js
function foo(a) {
  this.a = a;
}
const bar = new foo(2);
console.log(bar.a); // 2
```

**规则优先级：** new 绑定 > 显式绑定 （call、apply、bind）> 隐式绑定（绑定上下文 obj 等调用函数）> 默认绑定
**特殊情况：** 在某些场景下 this 的绑定行为会出乎意料，你认为应当应用其他绑定规则时，实际上应用的可能是**默认绑定规则**。**更安全**的编码是将 this 显式绑定到 null、undefined（等同于默认绑定 window 等）或者一个特殊对象：
call 默认绑定：

```js
function foo() {
  console.log(this.a);
}
const a = 2;
foo.call(null); // 2
```

apply 和 bind 柯里化：默认绑定

```js
function foo(a, b) {
  return a + b;
}
var fn = foo.bind(null, 3);
foo.apply(null, [4, 5]);
fn(4);
```

有些调用可能在无意中使用默认**绑定规则**。如果想“更安全”地忽略 this 绑定，你可以使用一个**DMZ 对象**，比如:

```js
o = Object.create(null);
```

以保护全局对象

**ES6 中的箭头函数并不会使用四条标准的绑定规则，而是根据当前的词法作用域来决定 this，具体来说，箭头函数会继承外层函数调用的 this 绑定（无论 this 绑定到什么）。这其实和 ES6 之前代码中的 self = this 机制一样。**

```js
function foo() {
  setTimeout(() => {
    // 这里的this 在此法上继承自foo()
    console.log(this.a);
  }, 100);
}
const obj = {
  a: 2,
};
foo.call(obj); // 2
```

等同于：

```js
function foo() {
  const self = this; // lexical capture of this
  setTimeout(function () {
    console.log(self.a);
  }, 100);
}
const obj = {
  a: 2,
};
foo.call(obj); // 2
```
