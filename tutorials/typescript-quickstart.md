# TypeScript 快速入门（面向 Java / Kotlin 开发者）

TypeScript 在 JavaScript 之上增加了静态类型系统，编译后产出普通 JavaScript。语法基础与 JavaScript 一致，下面只讲类型相关的增量部分，建议先看 JavaScript 教程。

## 1. 类型标注

```ts
let count: number = 10;
let name: string = "Ann";
let active: boolean = true;
let ids: number[] = [1, 2, 3];         // 数组
let pair: [string, number] = ["a", 1]; // 元组，固定长度与类型
```

- 类型写在变量或参数名之后，用冒号分隔，写法与 Kotlin 一致。
- 大多数情况类型可省略，编译器自动推断：`let x = 10` 会推断为 `number`。

## 2. 函数类型

```ts
function add(a: number, b: number): number {
  return a + b;
}

// 箭头函数带类型
const mul = (a: number, b: number): number => a * b;

// 可选参数用 ?，默认参数直接给值
function greet(name: string, title?: string): string {
  return title ? `${title} ${name}` : name;
}

// 无返回值用 void
function log(msg: string): void {
  console.log(msg);
}
```

## 3. interface 与 type

```ts
// interface 描述对象结构，类似 Kotlin 的 data class 字段约定
interface User {
  id: number;
  name: string;
  email?: string;          // 可选字段
  readonly role: string;   // 只读字段，类似 val
}

const u: User = { id: 1, name: "Ann", role: "admin" };

// type 别名，能力更广，可给任意类型命名
type ID = number | string;
type Point = { x: number; y: number };
```

`interface` 适合描述对象与类的形态，`type` 适合组合类型。两者在描述对象时可互换。

## 4. 联合类型与字面量类型

```ts
type Status = "idle" | "loading" | "done";   // 字面量联合，取值只能是这三个之一
let s: Status = "idle";

function format(input: number | string): string {
  if (typeof input === "number") {
    return input.toFixed(2);      // 此处编译器知道 input 是 number
  }
  return input.toUpperCase();     // 此处知道是 string
}
```

- 联合类型 `A | B` 表示取值属于 A 或 B，Java / Kotlin 没有直接对应。
- 通过 `typeof`、`in`、`instanceof` 做判断后，编译器会收窄类型（type narrowing）。

## 5. 泛型

```ts
// 泛型函数，写法与 Kotlin / Java 一致
function identity<T>(value: T): T {
  return value;
}

// 泛型约束，T 必须有 length 属性
function longest<T extends { length: number }>(a: T, b: T): T {
  return a.length >= b.length ? a : b;
}

// 泛型接口
interface Box<T> {
  value: T;
}
const box: Box<number> = { value: 1 };
```

## 6. 类

```ts
class Animal {
  protected name: string;         // 访问修饰符：public / private / protected

  constructor(name: string) {
    this.name = name;
  }

  speak(): string {
    return `${this.name} 发出声音`;
  }
}

class Dog extends Animal {
  speak(): string {
    return `${this.name} 汪汪`;
  }
}

// 实现接口，用 implements，与 Java 一致
interface Runnable {
  run(): void;
}
class Task implements Runnable {
  run(): void { /* ... */ }
}
```

字段可用简写：在构造函数参数前加修饰符即可自动成为字段，但本项目的编译配置（Node strip-only 模式）禁用该写法，需显式声明字段并在构造函数内赋值。

## 7. 枚举与常量联合

```ts
enum Color { Red, Green, Blue }        // 传统枚举，类似 Java 的 enum
let c: Color = Color.Red;

// 更常用的替代写法：字面量联合 + as const
const Colors = ["red", "green", "blue"] as const;
type ColorName = typeof Colors[number]; // "red" | "green" | "blue"
```

## 8. 空值与严格模式

开启 `strictNullChecks` 后，`null` 与 `undefined` 必须显式纳入类型：

```ts
let name: string | null = null;
let email: string | undefined;

function len(s: string | null): number {
  return s?.length ?? 0;              // 可选链 + 空值合并
}

// 断言非空，确信不为空时使用，用错会在运行时崩溃
function shout(s: string | null) {
  return s!.toUpperCase();
}
```

这套机制对应 Kotlin 的可空类型 `String?`，编译期即可发现潜在的空值访问。

## 9. 工具类型

```ts
interface User { id: number; name: string; email: string; }

type PartialUser = Partial<User>;      // 所有字段变可选
type UserName = Pick<User, "name">;    // 只取部分字段
type NoEmail = Omit<User, "email">;    // 去掉部分字段
type ReadonlyUser = Readonly<User>;    // 所有字段只读
```

内置工具类型基于泛型实现，用于从已有类型派生新类型，减少重复定义。

## 10. 编译与运行

```bash
tsc script.ts        # 编译为 script.js
node script.js       # 执行产物

npx tsx script.ts    # 或用 tsx 直接运行 .ts 文件
```

`tsconfig.json` 控制编译选项，常开 `"strict": true` 获得完整的类型检查。

