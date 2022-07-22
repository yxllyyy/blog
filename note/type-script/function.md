## 函数类型

在 JavaScript 开发中，函数是重要的组成部分，并且函数可以作为一等公民（可以作为参数，也可以作为返回值进
行传递）
那么在使用函数的过程中，函数是否也可以有自己的类型呢？

- 我们可以编写函数类型的表达式（Function Type Expressions），来表示函数类型

```ts
// 这可不是箭头函数哦,函数类型的语法就是这样的
type FnType = () => void;
function foo(fn: FnType) {}
```

## 参数的可选类型

我们可以指定某个参数是可选的：

```ts
function foo(x: string, y?: number) {}

foo("tao");
foo("tao", 18);
```

## 默认参数

从 ES6 开始，JavaScript 是支持默认参数的，TypeScript 也是支持默认参数的：

```ts
function foo(x?: string, y: number = 10) {}

foo("tao");
```

这个时候 y 的类型其实是 undefined 和 number 类型的
联合

## 剩余参数

TypeScript 也支持剩余参数,剩余参数语法允许我们将一个不定数量的参数放到一个数组中

```ts
function foo(...datas: number[]) {
  let result = 0;
  for (const data of datas) {
    result += data;
  }
  return result;
}

console.log(foo(10, 20, 30)); // 60
```

> 我个人传参顺序是必传参数>可选参数>剩余参数

## 在函数中声明 this (Declaring this in a Function)

我们先来看一个例子：

```ts
const info = {
  name: "tao",
  age: 18,
  foo() {
    console.log(this.name);
  },
};

info.foo();
```

上面的代码是可以正常运行的，也就是 TypeScript 在编译时，认为我们的 this 是可以正确去使用的：

- ts 可以理解 info.foo 中的 this 指向的是外层对象 info
- 但还是有一些情况需要你明确的告诉 TypeScript this 到底代表的是什么

我们来看这段代码:

```ts
function foo() {
  console.log(this.name); // Error: this'隐含着'any'的类型，因为它没有类型注释。
}

const info = {
  name: "tao",
  age: 18,
  foo,
};

info.foo();
```

这段代码运行会报错的：

- 这里我们再次强调一下，TypeScript 进行类型检测的目的是让我们的代码更加的安全
- 所以这里对于 foo 的调用来说，我们虽然将其放到了 info 中，通过 info 去调用，this 依然是指向 info 对象的
- 但是对于 TypeScript 编译器来说，这个代码是非常不安全的，因为我们也有可能直接调用函数，或者通过别的对象来
  调用函数
- 这个时候，通常 TypeScript 会要求我们明确的指定 this 的类型：

```ts
type InfoThis = {
  name: string;
  age: number;
};

function foo(this: InfoThis) {
  console.log(this.name);
  console.log(this.age);
}

const info = {
  name: "tao",
  age: 18,
  foo,
};

// 隐式绑定this为info
info.foo();
// 那我们想直接调佣foo行不行
// foo() // Error: 上下文的this类型为void
// 我们只能显式绑定this
foo.call({ name: "tao", age: 18 });
foo.apply({ name: "sandy", age: 21 });
```

## 函数重载

在 TypeScript 中，如果我们编写了一个 add 函数，希望可以对字符串和数字类型进行相加，应该如何编写呢？

```ts
// 虽然进行了类型缩小可以实现功能,但是我们无法确定返回值的类型
function foo(x: number | string, y: number | string) {
  if (typeof x === "string" && typeof y === "string") {
    return x + y;
  }
  if (typeof x === "number" && typeof y === "number") {
    return x + y;
  }
}
```

那么这个代码应该如何去编写呢？

- 在 TypeScript 中，我们可以去编写不同的重载签名（overload signatures）来表示函数可以以不同的方式进行
  调用
- 一般是编写两个或者以上的重载签名，再去编写一个通用的函数以及实现

在我们调用 foo 的时候，它会根据我们传入的参数类型来决定执行函数体时，到底执行哪一个函数的重载签名

```ts
// 重载签名
function foo(x: number, y: number): number;
function foo(x: string, y: string): string;

// 函数的实现
function foo(x: any, y: any): any {
  if (typeof x === "string") {
    return x + y;
  } else {
    return x + y;
  }
}

const res1 = foo(10, 20);
const res2 = foo("abc", "cba");
console.log(res1);
console.log(res2);
```

> 当使用联合类型实现不了的场景,我们就可以试着使用函数重载

但是注意，函数参数只能传入重载签名规定传入的参数

```ts
function foo(x: number, y: number): number;
function foo(x: string, y: string): string;

function foo(x: any, y: any): any {
  if (typeof x === "string") {
    return x + y;
  } else {
    return x + y;
  }
}

foo("12321", true); // Error: 没有与此调用匹配的重载
```
