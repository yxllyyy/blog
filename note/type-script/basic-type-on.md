## 原始类型(The original type): `string`、`number `和 `boolean`

TypeScript 中的原始类型和 JavaScript 一样

```ts
const message: string = "Hello World";
const age: number = 20;
const flag: boolean = true;

// ts会进行类型推导/推断(个人习惯是它能进行类型推导/推断就不写注解)
const message2 = "Hello World";
const age2 = 20;
const flag2 = true;
```

## 数组(Array)

数组类型定义有两种方式:

```ts
const arr1: Array<number> = [1, 2, 3]; // <>容易在react jsx冲突
const arr2: number[] = [1, 2, 3]; // 更推荐使用这种方式
```

## 对象(Object)

`object` 对象类型可以用于描述一个对象：

```ts
const info: object = {
  name: "tao",
  age: 18,
};

console.log("info.name", info.name); // Error: object类型上不存在name属性
```

在 TypeScript 中最好让它自己进行类型推导

```ts
const info = {
  name: "tao",
  age: 18,
};

console.log(info.name); // tao
```

## `Symbol`

TypeScript 中的 `Symbol` 和 JavaScript 一样

```ts
const s1 = Symbol("title");
const s2 = Symbol("title");
const info = {
  [s1]: "程序员",
  [s2]: "老师",
};
```

## `null`和`undefined`

在 TypeScript 中，它们各自的类型也是 `undefined` 和 `null`，也就意味着它们既是实际的值，也是自己的类型：

```ts
const n: null = null;
const u: undefined = undefined;
```

## `any`

在某些情况下，我们确实无法确定一个变量的类型，并且可能它会发生一些变化，这个时候我们可以使用 `any` 类型

`any` 类型有点像一种讨巧的 TypeScript 手段：

- 我们可以对 `any` 类型的变量进行任何的操作，包括获取不存在的属性、方法
- 我们给一个 `any` 类型的变量赋值任何的值，比如数字、字符串的值

如果对于某些情况的处理过于繁琐不希望添加规定的类型注解，或者在引入一些第三方库时，缺失了类型注解，这个时候
我们可以使用 `any：`

```ts
let message: any = "Hello World";
message = 123;
```

> `any` 使用过多,TypeScript -> anyScript

## `unknown`

unknown 是 TypeScript 中比较特殊的一种类型，它用于描述类型不确定的变量

```ts
function foo() {
  return "tao";
}

function bar() {
  return 123;
}

let flag = true;
// message的类型可能是string,也可能是number(对于我们来言是不清楚的)
// 可以使用any,但不推荐
// 对于不确定类型,我们就可以使用unknown
let message: unknown;
if (flag) {
  message = foo();
} else {
  message = bar();
}
```

那么 `any` 和 `unknown` 有什么区别喃?

- `any` 可以赋值给任意类型
- `unknown` 只能赋值给 `unknown/any` 类型

```ts
let message1: any;
let message2: unknown;

let result: number = message1;
let result2: number = message2; // Error: 不能将类型“unknown”分配给类型“number”
let result3: any = message2;
```

## `void`

`void`通常用来指定一个函数是没有返回值的，那么它的返回值就是`void`类型：

```ts
function foo(): void {
  console.log("aaa");
}

// 一般来说不写void,让它自己进行类型推导
function bar() {
  console.log("bbb");
}
```

如果指定了返回值为 void 类型,表明函数不能有返回值

- 我们也知道没有返回值表明返回 undefined

```ts
function foo(): void {
  return undefined;
}
```

## `never`

never 表示永远不会发生值的类型，比如一个函数：

- 如果一个函数中是一个死循环或者抛出一个异常，那么这个函数会返回东西吗？
- 不会，那么写 void 类型或者其他类型作为返回值类型都不合适，我们就可以使用 never 类型

```ts
function foo(): never {
  while (true) {
    console.log("aaa");
  }
}
```

never 有什么样的应用场景呢？这里我们举一个例子，但是它用到了联合类型，后面我们会讲到：

```ts
// | : 其中一个
function foo(param: string | number) {
  switch (typeof param) {
    case "string":
      console.log("执行string方法");
      break;
    case "number":
      console.log("执行number方法");
      break;
  }
}

foo("aaa");
foo(123);
// 我们只能传入string/number
foo(true); // Error: 类型“boolean”的参数不能赋给类型“string | number”的参数

// function foo(param: string | number | boolean) {
//   switch (typeof param) {
//     case 'string':
//       console.log('执行string方法')
//       break;
//     case 'number':
//       console.log('执行number方法')
//       break
//   }
// }
```

如果我们想传入其它的类型,那么别人是不是可以在参数里多加一种类型

- 比如多增加一个 boolean 类型
- 虽然是可以，但是如果别人增加了类型，但是从来没有在内部编写传入 boolean 类型需要做什么样的事情
- 这其实是不好的，最好给别人一个提示，如果增加了额外的类型，那么需要在内部编写对应的逻辑代码

```ts
// | : 其中一个
// 我们可以在内部的default里设置写一段神奇的代码
// result属于never类型,把字符串赋值给never类型,这肯定是报错的
// 所以如果别人在参数里多增加了类型
// 并且在内部没有对这个类型作处理
// 那么代码肯定会走到default,肯定会报错
// 别人看到报错了,就知道照着上面的逻辑写一个case处理对应的参数了
function foo(param: string | number | boolean) {
  switch (typeof param) {
    case "string":
      console.log("执行string方法");
      break;
    case "number":
      console.log("执行number方法");
      break;
    default:
      const result: never = "aaa";
  }
}

foo("aaa");
foo(123);
foo(true);
```

## `tuple`

`tuple`是元组类型，很多语言中也有这种数据类型，比如 Python、Swift 等

那么 tuple 和数组有什么区别呢？

- 首先，数组中通常建议存放相同类型的元素，不同类型的元素是不推荐放在数组中。（在数组中存放不同的元素推荐使用元祖）

```ts
const arr1: any[] = [1, "aaa", true];
const arr2: [number, string, boolean] = [1, "aaa", true];
console.log(arr1[0]); // 1
console.log(arr2[0]); // 1
```

那么 `tuple` 在什么地方使用的是最多的呢？

- tuple 通常可以作为返回的值，在使用的时候会非常的方便
- 这里使用 useState 做一个例子(react)
- 关于`<T>`和 `typeof` 后面会讲

```ts
function useState<T>(state: T) {
  let currentState = state;
  const setState = (newState: T) => {
    state = newState;
  };
  const tuple: [T, typeof setState] = [currentState, setState];
  return tuple;
}
```

## 函数的参数类型

函数是 JavaScript 非常重要的组成部分，TypeScript 允许我们指定函数的参数和返回值的类型

参数的类型注解

- 声明函数时，可以在每个参数后添加类型注解，以声明函数接受的参数类型：

```ts
function foo(num1: number, num2: number) {
  return num1 + num2;
}
```

## 函数的返回值类型

我们也可以添加返回值的类型注解，这个注解出现在函数列表的后面：

- 和变量的类型注解一样，我们通常情况下不需要返回类型注解，因为 TypeScript 会根据 return 返回值推断函数的
  返回类型：
- 某些第三方库处于方便理解，会明确指定返回类型，但是这个看个人喜好

```ts
function bar(num1: number, num2: number): number {
  return num1 + num2;
}

//  类型推导
function foo(num1: number, num2: number) {
  return num1 + num2;
}
```

## 匿名函数的参数(Arguments to an anonymous function)

匿名函数与函数声明会有一些不同：

- 当一个函数出现在 TypeScript 可以确定该函数会被如何调用的地方时；
- 该函数的参数会自动指定类型

```ts
const names = ["abc", "cba", "nba"];
names.map((value) => {
  value.split("");
});
```

我们并没有指定 value 的类型，但是 value 是一个 string 类型：

- 这是因为 TypeScript 会根据 map 函数的类型以及数组的类型推断出 item 的类型
- 这个过程称之为**上下文类型（contextual typing）**，因为函数执行的上下文可以帮助确定参数和返回值的类型

## 对象类型(Object type)

顾名思义,对象类型形式很像对象字面量

```ts
function foo(info: { name: string; age: number }) {
  console.log(info.x);
  console.log(info.y);
}
foo({ name: "tao", age: 18 });
```

## 可选类型(Optional type)

对于函数传入的参数我们可以指定那些是可选的,可以在参数后面加上一个`?`

```ts
function foo(name: string, age: number, flag?: boolean) {
  console.log("aaa");
}

foo("tao", 18, true);
foo("sandy", 21);
```

## 联合类型(The joint type)

联合类型是由两个或者多个其他类型组成的类型

- 表示可以是这些类型中的任何一个值
- 联合类型中的每一个类型被称之为联合成员

```ts
function foo(x: string | number) {
  console.log(x);
}

foo("abc");
foo(123);
```

传入给一个联合类型的值是非常简单的：只要保证是联合类型中的某一个类型的值即可

- 但是我们拿到这个值之后，我们应该如何使用它呢？因为它可能是任何一种类型
- 比如我们拿到的值可能是 string 或者 number，我们就不能对其调用 string 上的一些方法

那么我们怎么处理这样的问题呢？

- 我们需要使用类型缩小（后续我们还会专门讲解缩小相关的功能）
- TypeScript 可以根据我们缩小的代码结构，推断出更加具体的类型

```ts
function foo(x: string | number) {
  if (typeof x === "string") {
    console.log(x.length);
  } else {
    console.log(x);
  }
}

foo("abc");
foo(123);
```

其实上，可选类型可以看做是 类型 和 undefined 的联合类型：

```ts
function foo(x?: number) {
  console.log(x);
}
// 两者写法其实是相似的
// 只不过语法上有所不同,可选类型表明你必须明确传入其中一种类型
// function foo(x: number | undefined) {
//   console.log(x)
// }

foo(2);
foo(undefined);
```

## 字面量类型(Literal type)

除了前面我们所讲过的类型之外，也可以使用字面量类型

```ts
// 看上去message是一个string类型,实际上我们把鼠标移到message上
// message是一个'Hello World'类型
// 如何理解?
// 因为通过const关键字定义的原始类型值是无法改变的
const message = "Hello World";
```

那么这样做有什么意义呢？

- 默认情况下这么做是没有太大的意义的，但是我们可以将多个类型联合在一起

```ts
type Direction = "left" | "right" | "top";
function foo(direction: Direction) {
  console.log("方向为:", direction);
}
foo("left");
```

## 字面量推理(Literal reasoning)

我们来看下面的代码：

```ts
type Method = "GET" | "POST";
function request(url: string, method: Method) {}
const info = {
  url: "https://baidu.com",
  method: "GET",
};

request(info.url, info.method); // Error: 类型“string”的参数不能赋给类型“Method”的参数
```

因为类型推导会把 info 的类型变为对象类型,inof.url 的类型为 string,inof.method 的类型为 string

request 的 method 要求我们传入"GET"或者是"POST"这种字面量类型,所以我们传入一个 string 类型肯定是不行的

我们可以这样来做

第一种方法通过定义接口,明确指定 info 的类型(接口后面会讲)

```ts
type Method = "GET" | "POST";
function request(url: string, method: Method) {}

//
interface Info {
  url: string;
  method: Method;
}
const info: Info = {
  url: "https://baidu.com",
  method: "GET",
};

request(info.url, info.method);
```

第二种方法:通过 `as const` 断言

```ts
type Method = "GET" | "POST";
function request(url: string, method: Method) {}

// as const类型断言
// 作用: 宽泛的类型 -> 具体的类型
const info = {
  url: "https://baidu.com",
  method: "GET",
} as const;

request(info.url, info.method);
```
