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
