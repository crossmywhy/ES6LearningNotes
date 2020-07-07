# ES6LearningNotes https://es6.ruanyifeng.com/

## 1. let命令
### let(局部变量)； var(全局变量)
e.g. 
```javascript
var a = [];
for (var i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6](); // 10
```
注意这里数组`a`里实际存的`console.log(i);`，而循环最后i=10。
### for循环还有一个特别之处，就是设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域。
```javascript
for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}
// abc
// abc
// abc
```
### let不存在变量提升: var命令会发生“变量提升”现象
即var变量可以“先使用，再声明”，只不过使用时变量为`undefined`；
let变量必须先声明，否则报错ReferenceError。
### let的暂时性死区（temporal dead zone，简称 TDZ）: 
let命令声明变量之前，该变量都是不可用的。
e.g.
```javascript
function bar(x = y, y = 2) {
  return [x, y];
}
bar(); // 报错

// 不报错
var x = x;

// 报错
let x = x;
// ReferenceError: x is not defined
```

### let不允许在相同作用域内，重复声明同一个变量。

## 2. 块级作用域
```javascript
// 块级作用域内部的函数声明语句，建议不要使用
{
  let a = 'secret';
  function f() {
    return a;
  }
}

// 块级作用域内部，优先使用函数表达式
{
  let a = 'secret';
  let f = function () {
    return a;
  };
}

// 第一种写法，报错。ES6 的块级作用域必须有大括号，如果没有大括号，JavaScript 引擎就认为不存在块级作用域。
if (true) let x = 1;

// 第二种写法，不报错
if (true) {
  let x = 1;
}
```

 ## 3. const 命令
 const一旦声明变量，就必须立即初始化(只声明不赋值，就会报错)。
 const的作用域与let命令相同：只在声明所在的块级作用域内有效。
 const实际上保证的是变量指向的那个内存地址所保存的数据不得改动。
 ```javascript
 const foo = {};

// 为 foo 添加一个属性，可以成功
foo.prop = 123;
foo.prop // 123

// 将 foo 指向另一个对象，就会报错
foo = {}; // TypeError: "foo" is read-only
```
```javascript
const a = [];
a.push('Hello'); // 可执行
a.length = 0;    // 可执行
a = ['Dave'];    // 报错
 ```
用`Object.freeze`方法将对象冻结。

