# ES6LearningNotes https://es6.ruanyifeng.com/

1. 
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
<br>
### >for循环还有一个特别之处，就是设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域。
```javascript
for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}
// abc
// abc
// abc
```
<br>
### >var命令会发生“变量提升”现象:
即var变量可以“先使用，再声明”，只不过使用时变量为`undefined`；<br>
let变量必须先声明，否则报错ReferenceError。


 
