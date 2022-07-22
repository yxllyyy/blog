## 前言

我们来看这样一段代码

```ts
function foo(x: string | number) {
  console.log(x.length); // Error: 类型“string | number”上不存在属性“length”。类型“number”上不存在属性“length”
}
```

因为 number 是没有 length 属性的,我们想得是传入的是 string 类型,然后调用 string 的 length 属性

- 我们需要让编辑器明确知道我们传入的是 string 类型
- 方法就是让类型进行缩小

类型缩小有几种方式

- typeof
- 平等缩小(Equality shrinks)
- instanceof
- in

## typeof

TypeScript typeof === JavaScript typeof

```ts
function foo(x: string | number) {
  if (typeof x === "string") {
    // 如果能进行if语句中,编辑器就知道传入的x一定是string类型
    console.log(x.length);
  }
}
```

## 平等缩小(Equality shrinks)

Typescript 也会使用 switch 语句和等值检查比如 == !== == != 去收窄类型。比如：

```ts
function foo(x: string | number, y: boolean | string) {
  if (x === y) {
    // 如果x与y类型相同,那么x和y一定是string类型
    console.log(x.length);
  }
}
```

## instanceof

instanceof 来检查一个值是否是另一个值的“实例”

```ts
function foo(x: string | Date) {
  if (x instanceof Date) {
    console.log(x.toUTCString());
  }
}
```

## in

in 用于确定对象是否具有带名称的属性

```ts
type Student = {
  name: string;
  age: number;
};
type Teacher = {
  height: number;
};

function foo(x: Student | Teacher) {
  if ("height" in x) {
    console.log(x.height);
  }
}
```
