## 前言

TypeScript 作为 JavaScript 的超集，也是支持使用`class`关键字的，并且还可以对类的属性和方法等进行静态类型检测
实际上在 JavaScript 的开发过程中，我们更加习惯于函数式编程：

- 比如 React 开发中，目前更多使用的函数组件以及结合 Hook 的开发模式
- 比如在 Vue3 开发中，目前也更加推崇使用 Composition API
  但是在封装某些业务的时候，类具有更强大封装性，所以我们也需要掌握它们

## 类的定义

JavaScript class = TypeScript class

- 但是在 TypeScript 类中的属性是**必须进行初始化**的

```ts
class Person {
  name: string;
  age: number;
  // 可以在这里赋值,也可以在构造器中赋值(推荐)
  // name: string = 'tao'
  // age: number = 'age'
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
  sayHello() {
    console.log("Hello World");
  }
}
const p = new Person("tao", 18);
console.log(p.name); // tao
console.log(p.age); // 18
p.sayHello(); // Hello World
```

## 类的继承

面向对象的其中一大特性就是继承，继承不仅仅可以减少我们的代码量，也是多态的使用前提

```ts
class Person {
  name: string;
  age: number;
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
  sayHello() {
    console.log("Hello World");
  }
}
class Student extends Person {
  height: number;
  constructor(name: string, age: number) {
    super(name, age);
    this.height = this.height;
  }
  // 重写父类的sayHello方法
  sayHello() {
    console.log("student sayHello");
  }
}
const p = new Student("tao", 18);
console.log(p.name); // tao
console.log(p.age); // 18
p.sayHello(); // student sayHello
```

## 类的成员修饰符

跟 JavaScript 一样

```ts
class Person {
  public name: string; // 公共(默认)
  private age: number; // 私有(只能在自身内部访问)
  protected height: number; // 受保护的(只能在自身内部或者子类中使用)
  constructor(name: string, age: number, height: number) {
    this.name = name;
    this.age = age;
    this.height = height;
  }
  getAge() {
    return this.age;
  }
}

class Student extends Person {
  constructor(name: string, age: number, height: number) {
    super(name, age, height);
  }
  getHeight() {
    return this.height;
  }
}

const p = new Person("tao", 18, 1.88);
console.log(p.name);
// console.log(p.age)  // Error: 属性“age”为私有属性，只能在类“Person”中访问
console.log(p.getAge()); // 18

const s = new Student("sandy", 21, 1.65);
console.log(s.name);
// console.log(s.height) // Error: 属性“height”受保护，只能在类“Person”及其子类中访问
console.log(s.getHeight()); // 1.65
```

## 只读属性 readonly

如果有一个属性我们不希望外界可以任意的修改，只希望确定值后直接使用，那么可以使用 `readonly`

```ts
class Person {
  readonly name: string;
  // 只读属性是可以在构造器中初始化的
  constructor(name: string) {
    this.name = name;
  }
}
const p = new Person("tao");
console.log(p.name);
// p.name = 'sandy'  // 无法分配到 "name" ，因为它是只读属性
```

readonly 跟 const 很像,不能改变它的引用,如果是一个对象,那么是可以改变其对象的属性值的

```ts
type InfoType = {
  name: string;
  age: number;
};
class Person {
  readonly info: InfoType;
  // 只读属性是可以在构造器中初始化的
  constructor(info: InfoType) {
    this.info = info;
  }
}
const p = new Person({ name: "tao", age: 18 });
console.log(p.info.name); // tao
p.info.name = "sandy";
console.log(p.info.name); // sandy
```

## getters/setters

在前面一些私有属性我们是不能直接访问的，或者某些属性我们想要监听它的获取(getter)和设置(setter)的过程，
这个时候我们可以使用存取器。

```ts
class Person {
  //  #为私有属性的语法糖
  #name: string;
  constructor(name: string) {
    this.#name = name;
  }
  get name() {
    return this.#name;
  }
  set name(newName) {
    this.#name = newName;
  }
}
const p = new Person("tao");
console.log(p.name); // tao
p.name = "sandy";
console.log(p.name); // sandy
```

## 静态成员

类可以有静态成员，静态成员跟类实例没有关系，可以通过类本身访问到：

```ts
class Person {
  static age: number = 18;
  static sayHello() {
    console.log("Hello World");
  }
}
console.log(Person.age); // 18
Person.sayHello(); // Hello World
```

## 抽象类 abstract

我们知道，继承是多态使用的前提

- 所以在定义很多通用的调用接口时, 我们通常会让调用者传入父类，通过多态来实现更加灵活的调用方式
- 但是，父类本身可能并不需要对某些方法进行具体的实现，所以父类中定义的方法,，我们可以定义为抽象方法

什么是 抽象方法? 在 TypeScript 中没有具体实现的方法(没有方法体)，就是抽象方法

- 抽象方法，必须存在于抽象类中
- 抽象类是使用 abstract 声明的类

抽象类有如下的特点：

- 抽象类是不能被实例的话（也就是不能通过 new 创建）
- 抽象方法必须被子类实现，否则该类必须是一个抽象类

比如我们想实现传入一个函数,传入一个形状,然后输出对应的面积

```ts
class Rectangular {
  long: number;
  wide: number;
  constructor(long: number, wide: number) {
    this.long = long;
    this.wide = wide;
  }
  getArea() {
    return this.long * this.wide;
  }
}

class Square {
  sideHeight: number;
  constructor(sideHeight: number) {
    this.sideHeight = sideHeight;
  }
  getArea() {
    return this.sideHeight ** 2;
  }
}

function calculateArea(shape) {
  return Shape.getArea();
}
```

虽然我们实现了这个功能,但我们无法得知 shape 是什么类型的,那么就无法约束开发者在调用这个函数的时候乱传

这个时候我们可以使用抽象类让所有的形状都继承于抽象类

```ts
// 表达一个抽象类即在类的前面通过关键字abstract定义
abstract class Shape {
  // 抽象属性/方法: 抽象方法是不能有实现体,必须在子类中进行实现
  abstract getArea(): number;
}

class Rectangular extends Shape {
  long: number;
  wide: number;
  constructor(long: number, wide: number) {
    super();
    this.long = long;
    this.wide = wide;
  }
  // 必须在子类中实现抽象类的抽象属性/方法
  getArea() {
    return this.long * this.wide;
  }
}

class Square extends Shape {
  sideHeight: number;
  constructor(sideHeight: number) {
    super();
    this.sideHeight = sideHeight;
  }
  getArea() {
    return this.sideHeight ** 2;
  }
}

function calculateArea(shape: Shape) {
  return shape.getArea();
}

console.log(calculateArea(new Rectangular(2, 3))); // 6
console.log(calculateArea(new Square(10))); // 100
// new Shape() // Error: 无法创建抽象类的实例
```

## 类的类型

类本身也是可以作为一种数据类型的

```ts
class Person {
  name: string;
  age: number;
  sayHello() {}
}

// 必须实现类类型中的属性和方法
const info: Person = {
  name: "tao",
  age: 18,
  sayHello() {
    console.log("Hello World");
  },
};
```
