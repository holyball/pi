# JavaScript 快速入门（面向 Java / Kotlin 开发者）

JavaScript 是动态类型、解释执行的语言，变量的类型在运行时确定。下面每个语法点都附带与 Java / Kotlin 的对照。

## 1. 变量声明

```js
let count = 10;        // 可重新赋值，类似 Kotlin 的 var
const name = "Ann";    // 不可重新赋值，类似 Kotlin 的 val / Java 的 final
var old = 1;           // 老写法，作用域规则怪异，新代码不要用
```

- `let` 与 `const` 是块级作用域，规则与 Kotlin 的 `var` / `val` 一致。
- `const` 只保证引用不变，对象内部仍可修改：

```js
const list = [1, 2];
list.push(3);          // 允许，list 现在是 [1, 2, 3]
// list = [];          // 报错，不能重新赋值
```

## 2. 基本类型

```js
let n = 42;            // number，整数与浮点统一为一种类型
let big = 42n;         // bigint，超大整数
let s = "hello";       // string
let b = true;          // boolean
let u = undefined;     // 变量声明但未赋值的默认值
let z = null;          // 显式的空值
let sym = Symbol("k"); // 唯一标识
```

- 没有 Java 的 `int` / `long` / `double` 之分，数字都是 `number`（IEEE 754 双精度浮点）。
- `null` 与 `undefined` 是两个不同的空值：`undefined` 表示未赋值，`null` 表示人为置空。
- 用 `typeof x` 查看类型：`typeof 42` 得到 `"number"`。

## 3. 相等判断

```js
1 == "1"    // true，会先做类型转换，容易出错
1 === "1"   // false，同时比较值与类型
```

始终使用 `===` 与 `!==`，避免隐式类型转换带来的意外结果。

## 4. 函数

```js
// 函数声明
function add(a, b) {
  return a + b;
}

// 箭头函数，类似 Kotlin 的 lambda
const mul = (a, b) => a * b;
const square = x => x * x;      // 单参数可省略括号
const greet = () => "hi";       // 无参数保留空括号

// 默认参数
function power(base, exp = 2) {
  return base ** exp;
}

// 剩余参数，收集为数组，类似 Java 的可变参数
function sum(...nums) {
  return nums.reduce((acc, x) => acc + x, 0);
}
```

函数是一等公民，可以赋值给变量、当作参数传递、作为返回值。

## 5. 控制流

```js
if (count > 0) {
  console.log("正数");
} else if (count === 0) {
  console.log("零");
} else {
  console.log("负数");
}

// for-of 遍历值，类似 Kotlin 的 for (x in list)
for (const item of [10, 20, 30]) {
  console.log(item);
}

// for-in 遍历对象的键
const obj = { a: 1, b: 2 };
for (const key in obj) {
  console.log(key, obj[key]);
}

// while 与经典 for
let i = 0;
while (i < 3) { i++; }
for (let j = 0; j < 3; j++) { /* ... */ }

// switch
switch (count) {
  case 1: console.log("一"); break;
  default: console.log("其他");
}
```

## 6. 数组与对象

```js
// 数组，长度可变，元素类型不限
const arr = [1, "two", true];
arr.push(4);                      // 追加
arr.map(x => x);                  // 映射
arr.filter(x => typeof x === "number");
arr.forEach(x => console.log(x));

// 对象，键值集合，类似 Kotlin 的 Map，但语法更像结构体
const user = { id: 1, name: "Ann" };
user.email = "a@x.com";           // 动态添加属性
console.log(user.name);           // 点访问
console.log(user["id"]);          // 下标访问

// 解构，类似 Kotlin 的解构声明
const { id, name } = user;
const [first, second] = arr;
```

真正的键值容器用 `Map`：

```js
const m = new Map();
m.set("k", 1);
m.get("k");                       // 1
m.has("k");                       // true
```

## 7. 类与对象

```js
class Animal {
  constructor(name) {
    this.name = name;             // 字段直接在构造函数里赋值
  }
  speak() {
    return `${this.name} 发出声音`;
  }
}

class Dog extends Animal {
  constructor(name) {
    super(name);
  }
  speak() {
    return `${this.name} 汪汪`;    // 方法重写
  }
}

const d = new Dog("旺财");
console.log(d.speak());
```

- `class` 语法与 Java / Kotlin 接近，底层仍是原型继承。
- 用 `${}` 做字符串插值（模板字符串），类似 Kotlin 的 `$` 插值。

## 8. 空值处理

```js
const value = user.email ?? "默认邮箱";   // 空值合并，左侧为 null/undefined 时取右侧
const len = user?.address?.length;        // 可选链，中间为空时整体得到 undefined
```

- `?.` 类似 Kotlin 的 `?.`，避免访问空对象属性时崩溃。
- `??` 类似 Kotlin 的 `?:`，但只在 `null` / `undefined` 时生效，`0` 与 `""` 不触发。

## 9. 异步

```js
// Promise，代表未来才有的结果
function fetchData() {
  return new Promise((resolve) => {
    setTimeout(() => resolve("数据"), 1000);
  });
}

// async / await，写法类似 Kotlin 协程的 suspend 函数
async function main() {
  const data = await fetchData();
  console.log(data);
}
main();
```

`await` 只能在 `async` 函数内使用，等待 `Promise` 完成后取出结果。

## 10. 模块

```js
// math.js —— 导出
export function add(a, b) { return a + b; }
export const PI = 3.14;
export default class Calc {}

// main.js —— 导入
import Calc, { add, PI } from "./math.js";
```

- `export` / `import` 是标准模块语法，`import` 语句必须写在文件顶层。
- 每个文件是独立模块，作用域彼此隔离。

## 运行方式

```bash
node script.js      # 用 Node.js 执行单个文件
```

浏览器中通过 `<script type="module" src="main.js"></script>` 引入。

