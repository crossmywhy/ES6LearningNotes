# ES6 LearningNotes https://es6.ruanyifeng.com/

# 目录
[二、let 和 const 命令](#二let-和-const-命令)  
[三、变量的解构赋值](#三变量的解构赋值)  
[四、字符串的扩展](#四字符串的扩展)  
[五、字符串的新增方法](#五字符串的新增方法)  
[六、正则的扩展](#六正则的扩展)  
[十六、Promise对象](#十六Promise对象)  
[二十三、Module的语法](#二十三Module的语法)  
[杂学](#杂学)  
[二十五、编程风格](#二十五编程风格)  
[二十六、读懂ECMAScript规格](#二十六读懂ECMAScript规格)  



# 二、let 和 const 命令
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

## 4. 顶层对象的属性
顶层对象，在浏览器环境指的是`window`对象，在 Node 指的是`global`对象。ES5 之中，顶层对象的属性与全局变量是等价的。
```javascript
var a = 1;
// 如果在 Node 的 REPL 环境，可以写成 global.a
// 或者采用通用方法，写成 this.a
window.a // 1

let b = 1;
window.b // undefined
```
ES6规定var命令和function命令声明的全局变量，依旧是顶层对象的属性；let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性。


# 三、变量的解构赋值
```javascript
let [a, b, c] = [1, 2, 3];
// 等同于：
let a = 1;
let b = 2;
let c = 3;

let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []
```
具有 Iterator 接口，都可以采用数组形式的解构赋值:
```javascript
let [x, y, z] = new Set(['a', 'b', 'c']);
x // "a"
```
默认值：
```javascript
let [x = 1] = [undefined];
x // 1

let [x = 1] = [null];
x // null

let [x = 1, y = x] = [];     // x=1; y=1
let [x = 1, y = x] = [2];    // x=2; y=2
```
```javascript

let { bar, foo } = { foo: 'aaa', bar: 'bbb' };
foo // "aaa"
bar // "bbb"

let { baz } = { foo: 'aaa', bar: 'bbb' };
baz // undefined

// 将Math对象的对数、正弦、余弦三个方法
let { log, sin, cos } = Math;

// 将console.log赋值到log变量
const { log } = console;
log('hello') // hello
```
对象的解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量。真正被赋值的是后者，而不是前者。
```javascript
let { foo: baz } = { foo: 'aaa'};
baz // "aaa"
foo // error: foo is not defined
```
```javascript
let obj = {
  p: [
    'Hello',
    { y: 'World' }
  ]
};

let { p: [x, { y }] } = obj;
x // "Hello"
y // "World"
p // undefined

let { p, p: [x, { y }] } = obj;
x // "Hello"
y // "World"
p // ["Hello", {y: "World"}]
```
字符串：
```javascript
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"

let {length : len} = 'hello';
len // 5
```
函数参数的解构赋值：
```javascript
[[1, 2], [3, 4]].map(([a, b]) => a + b);
// [ 3, 7 ]
```
```javascript
function move({x = 0, y = 0} = {}) {
  return [x, y];
}
move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, 0]
move({}); // [0, 0]
move(); // [0, 0]
```

为函数move的参数指定默认值，而不是为变量x和y指定默认值，所以会得到与前一种写法不同的结果：
```javascript
function move({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}
move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, undefined]
move({}); // [undefined, undefined]
move(); // [0, 0]
```
## 用途：
### 从函数返回多个值(有点python的味道了)
```javascript
// 返回一个数组
function example() {
  return [1, 2, 3];
}
let [a, b, c] = example();

// 返回一个对象
function example() {
  return {
    foo: 1,
    bar: 2
  };
}
let { foo, bar } = example();
```
### 提取 JSON 数据
### 遍历 Map 结构
```javascript
const map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for (let [key, value] of map) {
  console.log(key + " is " + value);
}
// first is hello
// second is world
```
### 输入模块的指定方法
加载模块时，往往需要指定输入哪些方法。解构赋值使得输入语句非常清晰。
(import时也能用到)
```javascript
const { SourceMapConsumer, SourceNode } = require("source-map");
```

# 四、字符串的扩展
## 字符的 Unicode 表示法
```javascript
"\u0061"        // "a"
"\uD842\uDFB7"  // "𠮷"。
```
这种表示法只限于码点在`\u0000`~`\uFFFF`之间的字符。超出这个范围的字符，必须用两个双字节的形式表示。
```javascript
"\u{20BB7}"     // "𠮷"
"\u{41}\u{42}\u{43}"    // "ABC"

let hello = 123;
hell\u{6F} // 123
```
```javascript
'\z' === 'z'        // true
'\172' === 'z'      // true
'\x7A' === 'z'      // true
'\u007A' === 'z'    // true
'\u{7A}' === 'z'    // true
```
## 用`for...of`遍历字符串
```javascript
let text = String.fromCodePoint(0x20BB7);
for (let i = 0; i < text.length; i++) {
  console.log(text[i]);
}
// " "
// " "
```
传统的for循环无法识别大于`0xFFFF`的码点。
```javascript
for (let i of text) {
  console.log(i);
}
// "𠮷"
```
## 模板字符串
```javascript
// 字符串中嵌入变量
let name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`
// "Hello Bob, how are you today?"

let greeting = `\`Yo\` World!`;
// "`Yo` World!"

let obj = {x: 1, y: 2};
`${obj.x + obj.y}`
// "3"
```
### 嵌套
```javascript
const tmpl = addrs => `
  <table>
  ${addrs.map(addr => `
    <tr><td>${addr.first}</td></tr>
    <tr><td>${addr.last}</td></tr>
  `).join('')}
  </table>
`;

// 使用方法：
const data = [
    { first: '<Jane>', last: 'Bond' },
    { first: 'Lars', last: '<Croft>' },
];

console.log(tmpl(data));
// <table>
//
//   <tr><td><Jane></td></tr>
//   <tr><td>Bond</td></tr>
//
//   <tr><td>Lars</td></tr>
//   <tr><td><Croft></td></tr>
//
// </table>
```

# 五、字符串的新增方法
## String.raw()
`String.raw()`方法可以作为处理模板字符串的基本方法，它会将所有变量替换，而且对斜杠进行转义，方便下一步作为字符串来使用。
```javascript
`Hi\u000A!`;
// "Hi
// !"

String.raw`Hi\u000A!`;
// 实际返回 "Hi\\u000A!"，显示的是转义后的结果 "Hi\u000A!"

String.raw`Hi\\n`
// 返回 "Hi\\\\n"，显示"Hi\\n"

String.raw`Hi\\n` === "Hi\\\\n" // true
```

## codePointAt()
汉字“𠮷”，码点为`0x20BB7`，UTF-16 编码为`0xD842` `0xDFB7`（十进制为`55362` `57271`）
```javascript
var s = "𠮷a";
s.length // 3
s.charCodeAt(0) // 55362
s.charCodeAt(1) // 57271

s.codePointAt(0) // 134071
s.codePointAt(0).toString(16) // "20bb7"
s.codePointAt(1) // 57271
s.codePointAt(2).toString(16) // "61"
```
为正确获取字符串中的正确位置，用`for...of`结构：
```javascript
let s = '𠮷a';
for (let ch of s) {
  console.log(ch.codePointAt(0).toString(16));
}
// 20bb7
// 61
```

## includes(), startsWith(), endsWith()
```javascript
let s = 'Hello world!';

s.startsWith('Hello') // true
s.startsWith('world', 6) // true
s.endsWith('Hello', 5) // true
s.includes('Hello', 6) // false
```

## repeat()
```javascript
'hello'.repeat(2) // "hellohello"
'na'.repeat(2.9) // "nana"。小数取整
'na'.repeat('3') // "nanana"。参数优先转换成数字
```

## padStart()，padEnd()
```javascript
'x'.padStart(5, 'ab') // 'ababx'
'x'.padStart(4, 'ab') // 'abax'

'x'.padEnd(5, 'ab') // 'xabab'
'x'.padEnd(4, 'ab') // 'xaba'

'xxx'.padStart(2, 'ab') // 'xxx'

'abc'.padStart(10, '0123456789')  // '0123456abc'

'x'.padStart(4) // '   x'
'x'.padEnd(4) // 'x   '

'12'.padStart(10, 'YYYY-MM-DD') // "YYYY-MM-12"
'09-12'.padStart(10, 'YYYY-MM-DD') // "YYYY-09-12"
```

## trimStart()，trimEnd()
```javascript
const s = '  abc  ';

s.trim() // "abc"
s.trimStart() // "abc  "
s.trimEnd() // "  abc"
```

# 六、正则的扩展
(回顾一下常见的regx，不全。https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions)
* `/^A/`：匹配开头为A的字符串；
* `/t$/`：匹配结尾为t的字符串；
* `/bo*/`：匹配存在b，且b之后有连续任意个o的字符串；(`*`等价于`{0,}`)
* `/a+/`：匹配有连续正数个a的字符串；(`+`等价于`{1,}`)
* `/e?/`：匹配有0个或1个e的字符串；(`?`等价于`{0,1}`)
** 紧跟在任何量词*、+、?或 {} 的后面，将会使量词变为非贪婪（匹配尽量少的字符）。e.g. 对 "123abc" 使用 /\d+/ 将会匹配 "123"；而使用 /\d+?/ 则只会匹配到 "1"。
* `/.n/`：匹配换行符之外的任何单个字符，加上n；
** e.g. 匹配'an' 和 'on'，但不匹配 'nay'。
* `/x|y/`：匹配x或y；
* `/a{2}/`：匹配连续出现2次的a；
* `/[abcd]/`：匹配方括号中任意字符，等价于`/[a-d]/`；
* `/[\b]/`：匹配一个退格(U+0008)；
* `/xx\b/`：匹配以xx结尾的字符；`/\bxx/`：匹配以xx开始的字符；
* `/\B../`：匹配不为边界的任意两个字符(..)；
* `/\d/`：匹配一个数字，等价于`/[0-9]/`；
* `/\D/`：匹配一个非数字字符，等价于`/[^0-9]/`；
* `\f`：匹配一个换页符 (U+000C);
* `\n`：匹配一个换行符 (U+000A);
* `\r`：匹配一个回车符 (U+000D);
* `/\s/`：匹配一个空白字符，包括空格、制表符、换页符和换行符;
* `/\w/`：匹配一个非单字字符（字母、数字或者下划线），等价于 `/[A-Za-z0-9_]/`；
** e.g. 匹配"$5.28,"中的 '5'。

# 十六、Promise对象
```javascript
const promise = new Promise(function(resolve, reject) {
  // ... some code
  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});

promise.then(function(value) {
  // success
}, function(error) {
  // failure
});
```
e.g.
```javascript
let promise = new Promise(function(resolve, reject) {
  console.log('Promise');
  resolve();
});

promise.then(function() {
  console.log('resolved.');
});

console.log('Hi!');

// 输出顺序：
// Promise
// Hi!
// resolved
```
首先输出的是Promise。然后，then方法指定的回调函数，将在当前脚本所有同步任务执行完才会执行，所以resolved最后输出。

```javascript
getJSON("/post/1.json").then(function(post) {
  return getJSON(post.commentURL);
}).then(function (comments) {
  console.log("resolved: ", comments);
}, function (err){
  console.log("rejected: ", err);
});
// 用箭头函数可缩写为：
getJSON("/post/1.json").then(
  post => getJSON(post.commentURL)
).then(
  comments => console.log("resolved: ", comments),
  err => console.log("rejected: ", err)
);
```
### Promise.prototype.catch()
```javascript
getJSON('/post/1.json').then(function(post) {
  return getJSON(post.commentURL);
}).then(function(comments) {
  // some code
}).catch(function(error) {
  // 处理前面三个Promise产生的错误
});
```
一般来说，不要在then()方法里面定义 Reject 状态的回调函数（即then的第二个参数），总是使用catch方法。

如果不用catch()方法指定错误处理的回调函数，Promise 对象抛出的错误不会传递到外层代码，即不会有任何反应：
```javascript
const someAsyncThing = function() {
  return new Promise(function(resolve, reject) {
    // 下面一行会报错，因为x没有声明
    resolve(x + 2);
  });
};

someAsyncThing().then(function() {
  console.log('everything is great');
});

setTimeout(() => { console.log(123) }, 2000);
// Uncaught (in promise) ReferenceError: x is not defined
// 123
```

### Promise.prototype.finally()
`finally()`方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。

### Promise.all()
`Promise.all()`方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。
```javascript
const p = Promise.all([p1, p2, p3]);
```
### p的状态由p1、p2、p3决定，分成两种情况：
* （1）只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。
* （2）只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。
  
注意，如果作为参数的 Promise 实例，自己定义了catch方法，那么它一旦被rejected，**并不会**触发Promise.all()的catch方法。
```javascript
const p1 = new Promise((resolve, reject) => {
  resolve('hello');
})
.then(result => result)
.catch(e => e);

const p2 = new Promise((resolve, reject) => {
  throw new Error('报错了');
})
.then(result => result)
.catch(e => e);   // 这里p2拥有catch方法。如果没有，就会由Promise的catch捕获错误。

Promise.all([p1, p2])
.then(result => console.log(result))
.catch(e => console.log(e));
// ["hello", Error: 报错了]
```

### Promise.race()
只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给p的回调函数。(用法与Promise.all()类似)

### Promise.any()
只要参数实例有一个变成fulfilled状态，包装实例就会变成fulfilled状态；如果所有参数实例都变成rejected状态，包装实例就会变成rejected状态。

### Promise.allSettled()
Promise.allSettled()方法接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例。只有等到所有这些参数实例都返回结果，不管是fulfilled还是rejected，包装实例才会结束。

```javascript
const resolved = Promise.resolve(42);
const rejected = Promise.reject(-1);

const allSettledPromise = Promise.allSettled([resolved, rejected]);

allSettledPromise.then(function (results) {
  console.log(results);
});
// [
//    { status: 'fulfilled', value: 42 },
//    { status: 'rejected', reason: -1 }
// ]
```
(Promise.allSettled()的返回值allSettledPromise，状态只可能变成fulfilled)

e.g.
```javascript
const promises = [ fetch('index.html'), fetch('https://does-not-exist/') ];
const results = await Promise.allSettled(promises);

// 过滤出成功的请求
const successfulPromises = results.filter(p => p.status === 'fulfilled');

// 过滤出失败的请求，并输出原因
const errors = results
  .filter(p => p.status === 'rejected')
  .map(p => p.reason);
```
```javascript

const urls = [ /* ... */ ];
const requests = urls.map(x => fetch(x));

try {
  await Promise.all(requests);
  console.log('所有请求都成功。');
} catch {
  console.log('至少一个请求失败，其他请求可能还没结束。');
}
```

### Promise.resolve() 
将现有对象转为 Promise 对象。  
其参数设置有四种情况：
* 参数是一个 Promise 实例：Promise.resolve将不做任何修改、原封不动地返回这个实例。

* 参数是一个`thenable`对象：
```javascript
let thenable = {
  then: function(resolve, reject) {
    resolve(42);
  }
};

let p1 = Promise.resolve(thenable);
p1.then(function(value) {
  console.log(value);  // 42
});
```

* 参数不是具有then方法的对象，或根本就不是对象：
```javascript
const p = Promise.resolve('Hello');

p.then(function (s){
  console.log(s)
});
// Hello
```

* 不带有任何参数：直接返回一个resolved状态的 Promise 对象。
e.g. 注意以下运行时序：
```javascript
setTimeout(function () {
  console.log('three');
}, 0);

Promise.resolve().then(function () {
  console.log('two');
});

console.log('one');

// one
// two
// three
```
上面代码中，setTimeout(fn, 0)在下一轮“事件循环”开始时执行，Promise.resolve()在本轮“事件循环”结束时执行，console.log('one')则是立即执行，因此最先输出。

### Promise.reject()
返回一个新的 Promise 实例，该实例的状态为rejected。


# 二十三、Module的语法
### export命令
模块功能：`export`命令用于规定模块的对外接口，`import`命令用于输入其他模块提供的功能。
```javascript
// profile.js
export var firstName = 'Michael';
var firstName = 'Michael';
var lastName = 'Jackson';
var year = 1958;
export { firstName, lastName, year };

function v1() { ... }
function v2() { ... }
export {
  v1 as streamV1,
  v2 as streamV2,
  v2 as streamLatestVersion
};
```
大括号指定所要输出的一组变量，`as`关键字重命名。  
  
export语句输出的接口，与其对应的值是动态绑定关系，即通过该接口，可以取到模块内部实时的值：
```javascript
export var foo = 'bar';
setTimeout(() => foo = 'baz', 500);
```
export命令不能处于块级作用域内，否则会报错。

### import命令
```javascript
// main.js
import { firstName, lastName, year } from './profile.js';
import { lastName as surname } from './profile.js';
```
import命令输入的变量不能修改、重新赋值，但可以改写变量属性：
```javascript
// main.js
import {a} from './xxx.js'
a = {};           // Syntax Error : 'a' is read-only;
a.foo = 'hello';  // 合法操作
```
! 但不建议这么改！！！
  
import是静态执行，所以不能使用表达式和变量。

### 模块的继承
假设有一个circleplus模块，继承了circle模块。
```javascript
// circleplus.js
export { area as circleArea } from 'circle';
```

### 跨模块常量
之前说`const`声明的常量只在当前代码块有效，但实际上可通过模块共享。如常量较多，可以建专门的`constants`目录，将各种常量写在不同的文件里面，保存在该目录下：
```javascript
// constants/db.js
export const db = {
  url: 'http://my.couchdbserver.local:5984',
  admin_username: 'admin',
  admin_password: 'admin password'
};

// constants/user.js
export const users = ['root', 'admin', 'staff', 'ceo', 'chief', 'moderator'];

// constants/index.js
export {db} from './db';
export {users} from './users';

// 使用时直接加载index.js
// script.js
import {db, users} from './constants/index';
```

### import()
`import`命令会被 JavaScript 引擎静态分析。ES2020提案 引入`import()`函数，支持动态加载模块。  
在需要的时候，再加载某个模块
```javascript
button.addEventListener('click', event => {
  import('./dialogBox.js')
  .then(dialogBox => {
    dialogBox.open();
  })
  .catch(error => {
    /* Error handling */
  })
});
  ```
`import()`也可以用在 async 函数之中、同时加载多个模块。
```javascript
async function main() {
  const myModule = await import('./myModule.js');
  const {export1, export2} = await import('./myModule.js');
  const [module1, module2, module3] =
    await Promise.all([
      import('./module1.js'),
      import('./module2.js'),
      import('./module3.js'),
    ]);
}
main();
```




# 杂学
## Basic Reducer Structure and State Shape
https://redux.js.org/recipes/structuring-reducers/basic-reducer-structure

### state
state是个plain Javascript object。存储方式决定应用核心。一般都将数据储存在“top-level key”：
```javascript
{
  visibilityFilter: 'SHOW_ALL',
  todos: [
    {
      text: 'Consider using Redux',
      completed: true,
    },
    {
      text: 'Keep all state in a single tree',
      completed: false
    }
  ]
}
```


# 二十五、编程风格
* 建议不再使用var命令，而是用let命令。
* 能用就多使用const，有利于提高程序的运行效率。
* 静态字符串一律使用单引号或反引号，不使用双引号。动态字符串使用反引号。
```javascript
const a = 'foobar';
const b = `foo${a}bar`;
```
* 对象尽量静态化，一旦定义，就不得随意添加新的属性。如果添加属性不可避免，要使用Object.assign方法。
```javascript
const a = { x: null };
a.x = 3;
```
* 用扩展运算符（...）拷贝数组。
```javascript
const itemsCopy = [...items];
```

# 二十六、读懂ECMAScript规格
规格是解决问题的“最后一招”，遇到疑难的语法问题，实在找不到答案，这时可以去查看规格文件。