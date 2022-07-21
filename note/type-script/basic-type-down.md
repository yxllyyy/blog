## 类型别名(Type the alias)

我们已经学会在类型注解里直接使用对象类型和联合类型，这很方便，但有的时候，一个类型会被使用多次，此时我们更希望通过一个单独的名字来引用它

顾名思义类型别名就是给类型起一个名称,通过`type`关键字

```ts
function foo(info: { name: string; age: number }) {
  console.log(info.name);
  console.log(info.age);
}

type InfoType = {
  name: string;
  age: number;
};

function bar(info: InfoType) {
  console.log(info.name);
  console.log(info.age);
}
```

## 类型断言 as

有时候 TypeScript 无法获取具体的类型信息，这个我们需要使用类型断言（Type Assertions）

- 比如我们通过 document.getElementById，TypeScript 只知道该函数会返回 HTMLElement ，但并不知道它
  具体的类型：

```ts
// HTMLElement -> HTMLImageElement
// 大范围 -> 小范围
const imgEl = document.querySelector(".img") as HTMLImageElement;
imgEl.src = "地址";
```

类型断言常见的应用场景

- 我们先来看一个例子

```ts
class Person {}

class Student extends Person {
  run() {
    console.log("run");
  }
}

// 因为Student是Person的子类
// 所以我们也可以把s传入到stuRun里来的
// 但是当你想调Student里的方法的时候
// ts只知道你传入的是Person类型
// Person类型上是不存在run的

function stuRun(p: Person) {
  p.run(); // Error: 类型“Person”上不存在属性“run”
}

const s = new Student();
stuRun(s);
```

所以我们需要把传入的 Person 类型转为具体的 Student 类型

```ts
class Person {}

class Student extends Person {
  run() {
    console.log("run");
  }
}

function stuRun(p: Person) {
  (p as Student).run();
}

const s = new Student();
stuRun(s);
```

TypeScript 只允许类型断言转换为 **更具体** 或者 **不太具体** 的类型版本，此规则可防止不可能的强制转换：

```ts
const message = "title";
const age1: number = message as any as number;
const age2: number = message as unknown as number;
```

## 非空类型断言!

当我们编写下面的代码时，在执行 ts 的编译阶段会报错：

- 这是因为传入的 message 有可能是为 undefined 的，这个时候是不能执行方法的

```ts
function foo(message?: string) {
  console.log(message.length);
}
foo();
```

但是，我们确定传入的参数是有值的，这个时候我们可以使用非空类型断言：

- 非空断言使用的是 ! ，表示**可以确定某个标识符是有值的**，跳过 ts 在编译阶段对它的检测

```ts
function foo(message?: string) {
  console.log(message!.length);
}
// 必须要求传入string,不能传入undefined或者不传
foo("111");
// foo()
```

## 可选链?.

可选链并不是 TypeScript 独有的,它是 ES11（ES2020）中增加的特性：

具体如何使用详情跳转-> 暂定
