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
