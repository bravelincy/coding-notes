# Typescript学习笔记
> Typescript是微软推出的强类型Javascript语言，它是Javascript的超集，可以编译成纯粹的Javascript运行在任何系统中。

## 安装
官方提供了两种方式进行安装Typescript：

1. 通过Node.js的包管理器`npm`来安装
2. 通过安装vscode编辑器的插件（没试过）

推荐使用`npm`来安装，非常简单：`npm install -g typescript`  
安装成功后就可以通过`tsc`命令行工具来编译`.ts`文件，比如`tsc greeter.ts`会在同目录下编译生成greeter.js

## 语法
### 数据类型
> 如你所料，Typescript不仅仅支持Javascript中的数据类型，比如数值、字符串、布尔值等等，还支持更多新的类型。

#### 布尔值（Boolean）

``` javascript
let isDone: boolean = false;
```

#### 数值（Number）
和Javascript一样，所有数值在Typescript中都是浮点型。除了十六进制和十进制外，还支持了ECMAScript 2015增加的二进制和八进制数值字面量。

``` javascript
let decimal: number = 6;
let hex: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;
```

#### 字符串（String）

``` javascript
let color: string = "blue";
color = 'red';
```

你还可以使用模板字符串，这样就可以使用换行了，你需要将它们用`` ` ``包起来，而且支持通过`${ expr }`来嵌套表达式：

``` javascript
let fullName: string = `Bob Bobbington`;
let age: number = 37;
let sentence: string = `Hello, my name is ${ fullName }.

I'll be ${ age + 1 } years old next month.`
```

这和以下声明是相等的：

``` javascript
let sentence: string = "Hello, my name is " + fullName + ".\n\n" +
    "I'll be " + (age + 1) + " years old next month."
```

#### 数组（Array）
数组类型可是通过两种方式声明，一是在数组元素类型后面加一对`[]`来标记数组中的元素类型：

``` javascript
let list: number[] = [1, 2, 3];
```

二是使用普通的数组类型，`Array<elemType>`：

``` javascript
let list: Array<number> = [1, 2, 3];
```

#### 元组（Tuple）
元组类型允许你表示一个已经知道了一定数量元素类型的数组，但不需要一样。例如，你可能想要描述一对字符串和数字：

``` javascript
// Declare a tuple type
let x: [string, number];
// Initialize it
x = ["hello", 10]; // OK
// Initialize it incorrectly
x = [10, "hello"]; // Error
```

当通过一个已知索引来访问一个元素时，会取回正确的类型：

``` javascript
console.log(x[0].substr(1)); // OK
console.log(x[1].substr(1)); // Error, 'number' does not have 'substr'
```

当访问一个不在已知索引范围内的元素时，类型会变成一个类型组合：

``` javascript
x[3] = "world"; // OK, 'string' can be assigned to 'string | number'

console.log(x[5].toString()); // OK, 'string' and 'number' both have 'toString'

x[6] = true; // Error, 'boolean' isn't 'string | number'
```

#### 枚举（Enum）
枚举是一个非常有用的新特性，就像C#中一样，枚举提供了一种方式为数字集合中的每个数设置了一个名字。

``` javascript
enum Color {Red, Green, Blue};
let c: Color = Color.Green; // 1
```

默认地，一个枚举从0开始编号，当然你也可以手动地给枚举成员设定值。例如，我们可以让之前的例子从1开始：

``` javascript
enum Color {Red = 1, Green, Blue};
let c: Color = Color.Green; // 2
```

或者，干脆给每个成员都设定值，这也是可以的：

``` javascript
enum Color {Red = 1, Green = 2, Blue = 4};
let c: Color = Color.Green;
```

一个便利的特性是，你也可以通过数值获取它在枚举中对应的名字。例如，如果不确定2在上面的`Color`枚举中是什么名字与之对应，我们可以这样来检索：

``` javascript
enum Color {Red = 1, Green, Blue};
let colorName: string = Color[2];

alert(colorName); // 'Green'
```

#### 任意类型（Any）
有时候我们需要声明一个不知道类型的变量，比如这些值可能来自第三方模块。`any`类型可以选择性地退出类型检查以便编译时能够通过。

``` javascript
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean
```

`any`类型对于已有的Javascript代码是一个不错的选择，它允许你在编译过程中慢慢摸索类型检查。你可能希望`Object`和其它语言中的作用一样，但类型为`Object`的变量只允许赋值任何类型的值给它们，没法在它们上面任意调用方法，即使是一个切实存在的方法：

``` javascript
let notSure: any = 4;
notSure.ifItExists(); // okay, ifItExists might exist at runtime
notSure.toFixed(); // okay, toFixed exists (but the compiler doesn't check)

let prettySure: Object = 4;
prettySure.toFixed(); // Error: Property 'toFixed' doesn't exist on type 'Object'.
```

还有一个方便之处就是，用来声明任意类型元素的数组：

``` javascript
let list: any[] = [1, true, "free"];

list[1] = 100;
```

#### 空值（Void）
`void`就像`any`的对立面：没有任何类型。因此，你可能会经常看见这种没有返回值的函数的写法：

``` javascript
function warnUser(): void {
    alert("This is my warning message");
}
```

声明`void`类型的变量并没有什么卵用，因为你只能赋值`undefined`或`null`给它：

``` javascript
let unusable: void = undefined;
```

#### 类型断言
类型断言有两种形式，一是尖括号语法：

``` javascript
let someValue: any = "this is a string";

let strLength: number = (<string>someValue).length;
```

另一种是`as`语法：

``` javascript
let someValue: any = "this is a string";

let strLength: number = (someValue as string).length;
```

至于选择哪种是喜好问题，但是当Typescript和JSX一起用时，只有`as`方式可以用。

### 变量声明
> Typescript作为Javascript的超集，所以自然支持`let`和`const`声明。

