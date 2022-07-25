## 接口(interface)

我们通过 type 可以用来声明一个对象类型：

```ts
type info = {
  name: string;
  age: number;
};
```

对象的另外一种声明方式就是通过接口来声明：

```ts
interface IInfo {
  name: string;
  age: number;
}
```

接口中我们也可以定义可选属性和只读属性

```ts
interface IInfo {
  name: string;
  readonly age: number;
  height?: number;
}

const info: IInfo = {
  name: "tao",
  age: 18,
};

info.age = 123; // Error: 无法分配到 "age" ，因为它是只读属性
```

## 索引类型

有的时候，你不能提前知道一个类型里的所有属性的名字，但是你知道这些值的特征。

```ts
const info1 = {
  0: "HTML",
  1: "CSS",
  2: "JavaScript",
};

const info2 = {
  macbook: 9.9,
  macair: 3.2,
  macbookpro: 11.2,
};
```

我们如何指定上面两个对象的类型喃?

- 这种情况，你就可以用一个索引签名 (index signature) 来描述可能的值的类型，举个例子：

```ts
interface IInfo1 {
  // 你可能有次疑问?
  // 为什么这里的index类型是string
  // 0,1,2慢慢是number类型啊
  // 这是因为当使用一个数字进行索引的时候，ts/js实际上把它转成了一个字符串
  // 0 -> '0'
  [index: string]: string;
}
interface IInfo2 {
  [name: string]: number;
}

const info1: IInfo1 = {
  0: "HTML",
  1: "CSS",
  2: "JavaScript",
};

const info2: IInfo2 = {
  macbook: 9.9,
  macair: 3.2,
  macbookpro: 11.2,
};
```

## 接口继承

接口和类一样是可以进行继承的，也是使用 extends 关键字：

- 并且我们会发现，接口是支持多继承的（类不支持多继承）

```ts
interface IFoo {
  foo: () => void;
}
interface IBar {
  bar: () => void;
}

interface IBaz extends IFoo, IBar {
  baz: () => void;
}

const info: IBaz = {
  foo() {},
  bar() {},
  baz() {},
};
```

## 接口的实现

实现（implements）是面向对象中的一个重要概念。一般来讲，一个类只能继承自另一个类，有时候不同类之间可以有一些共有的特性，这时候就可以把特性提取成接口（interfaces），用 implements 关键字来实现。这个特性大大提高了面向对象的灵活性

举例来说，门是一个类，防盗门是门的子类。如果防盗门有一个报警器的功能，我们可以简单的给防盗门添加一个报警方法。这时候如果有另一个类，车，也有报警器的功能，就可以考虑把报警器提取出来，作为一个接口，防盗门和车都去实现它：

```ts
interface Alarm {
  alert(): void;
}

class Door {}

class SecurityDoor extends Door implements Alarm {
  alert() {
    console.log("SecurityDoor alert");
  }
}

class Car implements Alarm {
  alert() {
    console.log("Car alert");
  }
}
```

一个类可以实现多个接口：

```ts
interface Alarm {
  alert(): void;
}

interface Light {
  lightOn(): void;
  lightOff(): void;
}

class Car implements Alarm, Light {
  alert() {
    console.log("Car alert");
  }
  lightOn() {
    console.log("Car light on");
  }
  lightOff() {
    console.log("Car light off");
  }
}
```

转载于: https://ts.xcatliu.com/advanced/class-and-interfaces.html#%E7%B1%BB%E5%AE%9E%E7%8E%B0%E6%8E%A5%E5%8F%A3

## 交叉类型

顾名思义示需要满足多个类型,使用 & 符号

```ts
interface IFoo {
  foo: () => void;
}
interface IBar {
  bar: () => void;
}

const info: IFoo & IBar = {
  foo() {},
  bar() {},
};
```

## interface 和 type 区别

我们会发现 interface 和 type 都可以用来定义对象类型，那么在开发中定义对象类型时，到底选择哪一个呢？

- 如果是定义非对象类型，通常推荐使用 type
- 如果是定义对象类型，那么他们是有区别的：
  - interface 可以重复的对某个接口来定义属性和方法(可扩展的)
  - 而 type 定义的是别名，别名是不能重复的

```ts
interface IFoo {
  foo: () => void;
}
interface IFoo {
  bar: () => void;
}

// 等价于

interface IFoo {
  foo: () => void;
  bar: () => void;
}
```

```ts
type Fn = (num1: number, num2: number) => number;
type Fn = (num1: number, num2: number) => number;

// Error: 标识符“Fn”重复
```

## 严格的对象字面量赋值

我们来看下面的代码：

```ts
interface IInfo {
  name: string;
  age: number;
}

const info: IInfo = {
  name: "tao",
  age: 18,
  height: 1.88,
};

// Error: height”不在类型“IInfo”中
```

这是因为 TypeScript 在字面量直接赋值的过程中，为了进行类型推导会进行严格的类型限制

但是如果我们是将一个 变量标识符 赋值给其他的变量时，会进行 `freshness` 擦除操作

```ts
interface IInfo {
  name: string;
  age: number;
}

const info2 = {
  name: "tao",
  age: 18,
  height: 1.88,
};

// 这种freshness操作只会让编译器在编译的时候不会报错,但是你如果想使用类型以外的属性当然是不行的
const info: IInfo = info2;
console.log(info.height);
// Error: 类型“IInfo”上不存在属性“height”
```

## 枚举类型

枚举类型是为数不多的 TypeScript 特性有的特性之一：

- 枚举其实就是将一组可能出现的值，一个个列举出来，定义在一个类型中，这个类型就是枚举类型
- 枚举允许开发者定义一组命名常量，常量可以是数字、字符串类型(跟联合类型很像)
- 使用 enum 关键字定义枚举

```ts
enum Direction {
  LEFT,
  RIGHT,
  BOTTOM,
  TOP,
}

function foo(direction: Direction) {
  switch (direction) {
    case Direction.LEFT:
      console.log("向左");
      break;
    case Direction.RIGHT:
      console.log("向右");
      break;
    case Direction.TOP:
      console.log("向上");
      break;
    case Direction.BOTTOM:
      console.log("向下");
      break;
    default:
      const p: never = direction;
      break;
  }
}
```

枚举类型默认是有值的，比如上面的枚举，默认值是这样的：

```ts
enum Direction {
  LEFT,
  RIGHT,
  BOTTOM,
  TOP,
}

console.log(Direction.LEFT); // 0
console.log(Direction.RIGHT); // 1
console.log(Direction.BOTTOM); // 2
console.log(Direction.TOP); // 3
```

当然，我们也可以给枚举其他值：

```ts
enum Direction {
  LEFT,
  RIGHT = 200,
  BOTTOM,
  TOP,
}

console.log(Direction.LEFT); // 0
console.log(Direction.RIGHT); // 200
console.log(Direction.BOTTOM); // 201
console.log(Direction.TOP); // 202
```

我们也可以给它们赋值其他的类型：

```ts
enum Direction {
  LEFT,
  RIGHT = "RIGHT",
  BOTTOM = "BOTTOM",
  TOP = "TOP",
}

console.log(Direction.LEFT); // 0
console.log(Direction.RIGHT); // RIGHT
console.log(Direction.BOTTOM); // BOTTOM
console.log(Direction.TOP); // TOP
```
