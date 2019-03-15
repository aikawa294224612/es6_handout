## ES6
[JavaScript ES6-重點紀錄 系列總集](https://ithelp.ithome.com.tw/articles/10197716)

from 阮一峰[ECMAScript 6 入门](http://es6.ruanyifeng.com/)

init:

    npm init -y
查看 Node 已經實現的 ES6 特性

    node --v8-options | findstr harmony
安装 Babel

    npm install --save-dev @babel/core
命令行工具@babel/cli

    npm install --save-dev @babel/cli
  
轉碼規則

    # 最新转码规则
    $ npm install --save-dev @babel/preset-env
    
    # react 转码规则
    $ npm install --save-dev @babel/preset-react
  
建立 .babelrc

    ren aa.txt .babelrc
    
加入至.babelrc

     {
        "presets": [
          "@babel/env",
          "@babel/preset-react"
        ],
        "plugins": []
      }
example.js:

    input.map(item => item + 1);

轉

    npx babel example.js --out-file compiled.js
    
轉碼後compiled.js:

    "use strict";
    
    input.map(function (item) {
      return item + 1;
    });
    
**Traceur**

    npm install -g traceur
    traceur example.js


    }
    
    console.log(foo());

### 🔅let 和 const 
**let**
for循環的計數器，就很合適使用let命令。

    for (let i = 0; i < 10; i++) {
      // ...
    }
    
    console.log(i);
    // ReferenceError: i is not defined

上面代碼中，計數器i只在for循環體內有效，在循環體外引用就會報錯。
另外，for循環還有一個特別之處，就是設置循環變量的那部分是一個父作用域，而循環體內部是一個單獨的子作用域。

    for (let i = 0; i < 3; i++) {
      let i = 'abc';
      console.log(i);
    }
    // abc
    // abc
    // abc

上面代碼正確運行，輸出了 3 次abc。這表明函數內部的變量i與循環變量i不在同一個作用域，有各自單獨的作用域。

**不存在變量提升**

    // var 的情況
    console.log(foo); // 輸出undefined
    var foo = 2;
    
    // let 的情況
    console.log(bar); // 報錯ReferenceError
    let bar = 2;
    
**暫時性死區（temporal dead zone，TDZ）**
只要塊級作用域內存在let命令，它所聲明的變量就“綁定”（binding）這個區域，不再受外部的影響
**在代碼塊內，使用`let`命令聲明變量之前，該變量都是不可用的**

    if (true) {
      // TDZ開始
      tmp = 'abc'; // ReferenceError
      console.log(tmp); // ReferenceError
    
      let tmp; // TDZ結束
      console.log(tmp); // undefined
    
      tmp = 123;
      console.log(tmp); // 123
    }
**不允許重復聲明**

    // 報錯
    function func() {
      let a = 10;
      var a = 1;
    }
    
    // 報錯
    function func() {
      let a = 10;
      let a = 1;
    }
**const**
const聲明一個只讀的常量。一旦聲明，常量的值就不能改變。

    const PI = 3.1415;
    PI // 3.1415
    
    PI = 3;
    // TypeError: Assignment to constant variable.
const一旦聲明變量，就必須立即初始化，不能留到以後賦值。

### 🔅變量的解構（Destructuring）賦值
從數組和對像中提取值，對變量進行賦值，稱為解構

    let [a, b, c] = [1, 2, 3];
    let [foo, [[bar], baz]] = [1, [[2], 3]];
    foo // 1
    bar // 2
    baz // 3
    
    let [ , , third] = ["foo", "bar", "baz"];
    third // "baz"
    
    let [x, , y] = [1, 2, 3];
    x // 1
    y // 3
    
    let [head, ...tail] = [1, 2, 3, 4];
    head // 1
    tail // [2, 3, 4]
    
    let [x, y, ...z] = ['a'];
    x // "a"
    y // undefined
    z // []
如果解構不成功，變量的值就等於undefined。

    let [foo] = [];
    let [bar, foo] = [1];

以上兩種情況都屬於解構不成功，foo的值都會等於undefined。
另一種情況是不完全解構，即等號左邊的模式，只匹配一部分的等號右邊的數組。這種情況下，解構依然可以成功。

    let [x, y] = [1, 2, 3];
    x // 1
    y // 2
    
    let [a, [b], d] = [1, [2, 3], 4];
    a // 1
    b // 2
    d // 4
如果等號的右邊不是數組(不可遍歷的結構，具Iterator)，那麼將會報錯

    // 报错
    let [foo] = 1;
    let [foo] = false;
    let [foo] = NaN;
    let [foo] = undefined;
    let [foo] = null;
    let [foo] = {};
對於Set 結構，也可以使用數組的解構賦值。

    let [x, y, z] = new Set(['a', 'b', 'c']);
    x // "a"

**默認值**

    let [foo = true] = [];
    foo // true
    
    let [x, y = 'b'] = ['a']; // x='a', y='b'
    let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'

只有當一個數組成員嚴格等於undefined，默認值才會生效。

    let [x = 1] = [undefined];
    x // 1
    
    let [x = 1] = [null];
    x // null

上面代碼中，如果一個數組成員是null，默認值就不會生效，因為null不嚴格等於undefined

    let [x = 1, y = x] = [];     // x=1; y=1
    let [x = 1, y = x] = [2];    // x=2; y=2
    let [x = 1, y = x] = [1, 2]; // x=1; y=2
    let [x = y, y = 1] = [];     // ReferenceError: y is not defined

上面最後一個表達式之所以會報錯，是因為x用y做默認值時，y還沒有聲明。

**對象解構**
解構不僅可以用於數組，還可以用於對象。

    let { foo, bar } = { foo: "aaa", bar: "bbb" };
    foo // "aaa"
    bar // "bbb"
對象的屬性沒有次序，變量必須與屬性同名，才能取到正確的值。

    let { bar, foo } = { foo: "aaa", bar: "bbb" };
    foo // "aaa"
    bar // "bbb"
    
    let { baz } = { foo: "aaa", bar: "bbb" };
    baz // undefined
對象的解構賦值的內部機制，是先找到同名屬性，然後再賦給對應的變量。真正被賦值的是後者，而不是前者。

    let obj = { first: 'hello', last: 'world' };
    let { first: f, last: l } = obj;
    f // 'hello'
    l // 'world'
    first //undefined
    last //undefined
與數組一樣，解構也可以用於嵌套結構的對象。

    let obj = {
      p: [
        'Hello',
        { y: 'World' }
      ]
    };
    
    let { p: [x, { y }] } = obj;
    x // "Hello"
    y // "World"
對象的解構也可以指定默認值。

    var {x = 3} = {};
    x // 3
    
    var {x, y = 5} = {x: 1};
    x // 1
    y // 5
    
    var {x: y = 3} = {};
    y // 3
    
    var {x: y = 3} = {x: 5};
    y // 5
    
    var { message: msg = 'Something went wrong' } = {};
    msg // "Something went wrong"
    
**字符串的解構賦值**

    const [a, b, c, d, e] = 'hello';
    a // "h"
    b // "e"
    c // "l"
    d // "l"
    e // "o"
類似數組的對像都有一個length屬性，因此還可以對這個屬性解構賦值。

    let {length : len} = 'hello';
    len // 5

**函數參數的解構賦值**

    function add([x, y]){
      return x + y;
    }
    
    add([1, 2]); // 3
下面是另一個例子。

    [[1, 2], [3, 4]].map(([a, b]) => a + b);
    // [ 3, 7 ]
    // [[1, 2], [3, 4]].map(function([a+b]){
    return a+b;
    });
函數參數的解構也可以使用默認值。

    function move({x = 0, y = 0} = {}) {
      return [x, y];
    }
    
    move({x: 3, y: 8}); // [3, 8]
    move({x: 3}); // [3, 0]
    move({}); // [0, 0]
    move(); // [0, 0]
注意，下面的寫法會得到不一樣的結果。

    function move({x, y} = { x: 0, y: 0 }) {
      return [x, y];
    }
    
    move({x: 3, y: 8}); // [3, 8]
    move({x: 3}); // [3, undefined]
    move({}); // [undefined, undefined]
    move(); // [0, 0]

**解構用途:**
**（1）交換變量的值**

    let x = 1;
    let y = 2;
    
    [x, y] = [y, x];

上面代碼交換變量x和y的值，這樣的寫法不僅簡潔，而且易讀，語義非常清晰

**（2）從函數返回多個值**
函數只能返回一個值，如果要返回多個值，只能將它們放在數組或對象裡返回。有了解構賦值，取出這些值就非常方便。

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
    
**（3）函數參數的定義**
解構賦值可以方便地將一組參數與變量名對應起來。

    // 参数是一组有次序的值
    function f([x, y, z]) { ... }
    f([1, 2, 3]);
    
    // 参数是一组无次序的值
    function f({x, y, z}) { ... }
    f({z: 3, y: 2, x: 1});

**（4）提取JSON 數據**
解構賦值對提取JSON 對像中的數據，尤其有用。

    let jsonData = {
      id: 42,
      status: "OK",
      data: [867, 5309]
    };
    
    let { id, status, data: number } = jsonData;
    
    console.log(id, status, number);
    // 42, "OK", [867, 5309]

**（5）函數參數的默認值**

    jQuery.ajax = function (url, {
      async = true,
      beforeSend = function () {},
      cache = true,
      complete = function () {},
      crossDomain = false,
      global = true,
      // ... more config
    } = {}) {
      // ... do stuff
    };

指定參數的默認值，就避免了在函數體內部再寫`var foo = config.foo || 'default foo';`這樣的語句。

**（6）遍歷Map 結構**

任何部署了Iterator接口的對象，都可以用`for...of`循環遍歷。Map結構原生支持Iterator接口，配合變量的解構賦值，獲取鍵名和鍵值就非常方便。

    const map = new Map();
    map.set('first', 'hello');
    map.set('second', 'world');
    
    for (let [key, value] of map) {
      console.log(key + " is " + value);
    }
    // first is hello
    // second is world

如果只想獲取鍵名，或者只想獲取鍵值，可以寫成下面這樣。

    // 获取键名
    for (let [key] of map) {
      // ...
    } 
    // 获取键值
    for (let [,value] of map) {
      // ...
    }

**（7）輸入模塊的指定方法**
加載模塊時，往往需要指定輸入哪些方法。解構賦值使得輸入語句非常清晰。

    const { SourceMapConsumer, SourceNode } = require("source-map");

### 🔅字串
**模板字串**
傳統的JavaScript 語言，輸出模板通常是這樣寫的（下面使用了jQuery 的方法）。

    $('#result').append(
      'There are <b>' + basket.count + '</b> ' +
      'items in your basket, ' +
      '<em>' + basket.onSale +
      '</em> are on sale!'
    );

上面這種寫法相當繁瑣不方便，ES6 引入了模板字符串解決這個問題。

    $('#result').append(`
      There are <b>${basket.count}</b> items
       in your basket, <em>${basket.onSale}</em>
      are on sale!
    `);

模板字符串（template string）是增強版的字符串，用反引號（`）標識。它可以當作普通字符串使用，也可以用來定義多行字符串，或者在字符串中嵌入變量。

    // 普通字符串
    `In JavaScript '\n' is a line-feed.`
    
    // 多行字符串
    `In JavaScript this is
     not legal.`
    
    console.log(`string text line 1
    string text line 2`);
    
    // 字符串中嵌入变量
    let name = "Bob", time = "today";
    `Hello ${name}, how are you ${time}?`

    //模板字符串之中還能調用函數。
    function fn() {
      return "Hello World";
    }
    
    `foo ${fn()} bar`
    // foo Hello World bar

上面代碼中的模板字符串，都是用反引號表示。如果在模板字符串中需要使用反引號，則前面要用反斜杠轉義。

    let greeting = `\`Yo\` World!`;
如果使用模板字符串表示多行字符串，所有的空格和縮進都會被保留在輸出之中。

    $('#list').html(`
    <ul>
      <li>first</li>
      <li>second</li>
    </ul>
    `);

上面代碼中，所有模板字符串的空格和換行，都是被保留的，比如`<ul>`標籤前面會有一個換行。如果你不想要這個換行，可以使用**trim**方法消除它。

    $('#list').html(`
    <ul>
      <li>first</li>
      <li>second</li>
    </ul>
    `.trim());

### 🔅數值
[數值的擴展](http://es6.ruanyifeng.com/#docs/number#Number-isFinite-Number-isNaN)

**Number.isFinite()/ Number.isNaN()**
Number.isFinite()用來檢查一個數值是否為有限的（finite），即不是Infinity。

    Number.isFinite(15); // true
    Number.isFinite(0.8); // true
    Number.isFinite(NaN); // false
    Number.isFinite(Infinity); // false
    Number.isFinite(-Infinity); // false
    Number.isFinite('foo'); // false
    Number.isFinite('15'); // false
    Number.isFinite(true); // false
Number.isNaN()用來檢查一個值是否為NaN。

    Number.isNaN(NaN) // true
    Number.isNaN(15) // false
    Number.isNaN('15') // false
    Number.isNaN(true) // false
    Number.isNaN(9/NaN) // true
    Number.isNaN('true' / 0) // true
    Number.isNaN('true' / 'true') // true
它們與傳統的全局方法isFinite()和isNaN()的區別在於，傳統方法先調用Number()將非數值的值轉為數值，再進行判斷，而這兩個新方法只對數值有效，**Number.isFinite()對於非數值一律返回false, Number.isNaN()只有對於NaN才返回true，非NaN一律返回false。**

    isFinite(25) // true
    isFinite("25") // true
    Number.isFinite(25) // true
    Number.isFinite("25") // false
    
    isNaN(NaN) // true
    isNaN("NaN") // true
    Number.isNaN(NaN) // true
    Number.isNaN("NaN") // false
    Number.isNaN(1) // false

**Number.parseInt()/Number.parseFloat()**
將字串轉成int和float，
ES6將全局方法parseInt()和parseFloat()，移植到Number對像上面，行為完全保持不變。

    // ES5的写法
    parseInt('12.34') // 12
    parseFloat('123.45#') // 123.45
    
    // ES6的写法
    Number.parseInt('12.34') // 12
    Number.parseFloat('123.45#') // 123.45

這樣做的目的，是逐步減少全局性方法，使得語言逐步模塊化。

    Number.parseInt === parseInt // true
    Number.parseFloat === parseFloat // true

**Number.isInteger（）**

    Number.isInteger(25) // true
    Number.isInteger(25.1) // false
    Number.isInteger(25.0) // true

如果一個數值的絕對值小於Number.MIN_VALUE（5E-324），即小於JavaScript能夠分辨的最小值，會被自動轉為0。這時，Number.isInteger也會誤判。

    Number.isInteger(5E-324) // false
    Number.isInteger(5E-325) // true

### 🔅函數
**函數參數的默認值**

    function log(x, y = 'World') {
      console.log(x, y);
    }
    
    log('Hello') // Hello World
    log('Hello', 'China') // Hello China
    log('Hello', '') // Hello
下面是另一個例子。

    function Point(x = 0, y = 0) {
      this.x = x;
      this.y = y;
    }
    const p = new Point();
    p // { x: 0, y: 0 }

參數p的默認值是x + 1。這時，每次調用函數foo，**都會重新計算**x + 1，而不是默認p等於100。

    let x = 99;
    function foo(p = x + 1) {
      console.log(p);
    }
    
    foo() // 100
    
    x = 100;
    foo() // 101

**與解構賦值默認值結合使用**

    function foo({x, y = 5}) {
      console.log(x, y);
    }
    
    foo({}) // undefined 5
    foo({x: 1}) // 1 5
    foo({x: 1, y: 2}) // 1 2
    foo() // TypeError: Cannot read property 'x' of undefined
上面代碼只使用了對象的解構賦值默認值，沒有使用函數參數的默認值。只有當函數foo的參數是一個對象時，變量x和y才會通過解構賦值生成。如果函數foo調用時沒提供參數，變量x和y就不會生成，從而報錯。通過提供函數參數的默認值，就可以避免這種情況。

    function foo({x, y = 5} = {}) {
      console.log(x, y);
    }  
    foo() // undefined 5

上面代碼指定，如果沒有提供參數，函數foo的參數默認為一個空對象。

**參數默認值的位置**
通常情況下，**定義了默認值的參數，應該是函數的尾參數**。如果非尾部的參數設置默認值，實際上這個參數是沒法省略的。

    // 例一
    function f(x = 1, y) {
      return [x, y];
    }
    
    f() // [1, undefined]
    f(2) // [2, undefined])
    f(, 1) // 报错
    f(undefined, 1) // [1, 1]
    
    // 例二
    function f(x, y = 5, z) {
      return [x, y, z];
    }
    
    f() // [undefined, 5, undefined]
    f(1) // [1, 5, undefined]
    f(1, ,2) // 报错
    f(1, undefined, 2) // [1, 5, 2]

**函數的length屬性**
指定了默認值以後，**函數的length屬性，將返回沒有指定默認值的參數個數**。

    (function (a) {}).length // 1
    (function (a = 5) {}).length // 0
    (function (a, b, c = 5) {}).length // 2

rest參數也不會計入length屬性。

    (function(...args) {}).length // 0

如果設置了默認值的參數不是尾參數，那麼length屬性也不再計入後面的參數了。

    (function (a = 0, b, c) {}).length // 0
    (function (a, b = 1, c) {}).length // 1

**rest參數**

    function add(...values) {
      let sum = 0;
    
      for (var val of values) {
        sum += val;
      }
      return sum;
    }
    
    add(2, 5, 3) // 10
下面是一個利用rest參數改寫數組push方法的例子。

    function push(array, ...items) {
      items.forEach(function(item) {
        array.push(item);
        console.log(item);
      });
    }
    
    var a = [];
    push(a, 1, 2, 3)
注意，rest 參數之後不能再有其他參數（即只能是最後一個參數），否則會報錯。

    // 报错
    function f(a, ...b, c) {
      // ...
    }
    
**name屬性**
函數的name屬性，返回該函數的函數名。

    function foo() {}
    foo.name // "foo"
如果將一個匿名函數賦值給一個變量，ES5的name屬性，會返回空字符串，而ES6的name屬性會返回實際的函數名。

    var f = function () {};
    
    // ES5
    f.name // ""
    
    // ES6
    f.name // "f"
Function構造函數返回的函數實例，name屬性的值為anonymous。

    (new Function).name // "anonymous"

bind返回的函數，name屬性值會加上bound前綴。

    function foo() {};
    foo.bind({}).name // "bound foo"
    
    (function(){}).bind({}).name // "bound "

**箭頭函數**

    var f = v => v;
    
    // 等同于
    var f = function (v) {
      return v;
    };
    
    var f = () => 5;
    // 等同于
    var f = function () { return 5 };
    
    var sum = (num1, num2) => num1 + num2;
    // 等同于
    var sum = function(num1, num2) {
      return num1 + num2;
    };
如果箭頭函數的代碼塊部分多於一條語句，就要使用大括號將它們括起來，並且使用return語句返回。

    var sum = (num1, num2) => { return num1 + num2; }
由於大括號被解釋為代碼塊，所以如果箭頭函數直接返回一個對象，必須在對像外面加上括號，否則會報錯。

    // 报错
    let getTempItem = id => { id: id, name: "Temp" };
    
    // 不报错
    let getTempItem = id => ({ id: id, name: "Temp" });
**箭頭函數有幾個使用注意點:**
1. 函數體內的this對象，就是定義時所在的對象，而不是使用時所在的對象。
2. 不可以當作構造函數，也就是說，不可以使用new命令，否則會拋出一個錯誤。
3. 不可以使用arguments對象，該對像在函數體內不存在。如果要用，可以用rest參數代替。
4. 不可以使用yield命令，因此箭頭函數不能用作Generator函數。

第一點尤其值得注意。this對象的指向是可變的，但是在箭頭函數中，它是固定的。

    function foo() {
      setTimeout(() => {
        console.log('id:', this.id);
      }, 100);
    }
    
    var id = 21;
    
    foo.call({ id: 42 });
    // id: 42

### 🔅陣列
[數組的擴展](http://es6.ruanyifeng.com/#docs/array)
:Array.from（）Array.of（）copyWithin() find() findIndex()
fill()  entries()，keys() 和values()  includes() flat()，flatMap()

**擴展運算符**
擴展運算符（spread）是三個點（...）。它好比rest參數的逆運算，將一個數組轉為用逗號分隔的參數序列。

    console.log(...[1, 2, 3])
    // 1 2 3
    
    console.log(1, ...[2, 3, 4], 5)
    // 1 2 3 4 5
    
    [...document.querySelectorAll('div')]
    // [<div>, <div>, <div>]
注意，擴展運算符如果放在括號中，JavaScript 引擎就會認為這是函數調用。如果這時不是函數調用，就會報錯。

    (...[1, 2])
    // Uncaught SyntaxError: Unexpected number
    
    console.log((...[1, 2]))
    // Uncaught SyntaxError: Unexpected number
    
    console.log(...[1, 2])
    // 1 2
一個例子是通過push函數，將一個數組添加到另一個數組的尾部。

    // ES5的 写法
    var arr1 = [0, 1, 2];
    var arr2 = [3, 4, 5];
    Array.prototype.push.apply(arr1, arr2);
    
    // ES6 的写法
    let arr1 = [0, 1, 2];
    let arr2 = [3, 4, 5];
    arr1.push(...arr2);
**擴展運算符的應用**
**（1）複製數組**
數組是複合的數據類型，直接複製的話，**只是複制了指向底層數據結構的指針，而不是克隆一個全新的數組。**

    const a1 = [1, 2];
    const a2 = a1;
    
    a2[0] = 2;
    a1 // [2, 2]

上面代碼中，a2並不是a1的克隆，而是指向同一份數據的另一個指針。修改a2，會直接導致a1的變化。
ES5 只能用變通方法來複製數組。

    const a1 = [1, 2];
    const a2 = a1.concat();
    
    a2[0] = 2;
    a1 // [1, 2]

擴展運算符提供了複製數組的簡便寫法。

    const a1 = [1, 2];
    // 写法一
    const a2 = [...a1];
    // 写法二
    const [...a2] = a1;

**（2）合併數組**
擴展運算符提供了數組合併的新寫法。

    const arr1 = ['a', 'b'];
    const arr2 = ['c'];
    const arr3 = ['d', 'e'];
    
    // ES5 的合并数组
    arr1.concat(arr2, arr3);
    // [ 'a', 'b', 'c', 'd', 'e' ]
    
    // ES6 的合并数组
    [...arr1, ...arr2, ...arr3]
    // [ 'a', 'b', 'c', 'd', 'e' ]

不過，這兩種方法都是淺拷貝，使用的時候需要注意。

    const a1 = [{ foo: 1 }];
    const a2 = [{ bar: 2 }];
    
    const a3 = a1.concat(a2);
    const a4 = [...a1, ...a2];
    
    a3[0] === a1[0] // true
    a4[0] === a1[0] // true

上面代碼中，a3和a4是用兩種不同方法合併而成的新數組，但是它們的成員都是對原數組成員的引用，這就是淺拷貝。如果修改了原數組的成員，會同步反映到新數組。

**（3）與解構賦值結合**
擴展運算符可以與解構賦值結合起來，用於生成數組。

    // ES5
    a = list[0], rest = list.slice(1)
    // ES6
    [a, ...rest] = list
下面是另外一些例子。
    
    const [first, ...rest] = [1, 2, 3, 4, 5];
    first // 1
    rest  // [2, 3, 4, 5]
    
    const [first, ...rest] = [];
    first // undefined
    rest  // []
    
    const [first, ...rest] = ["foo"];
    first  // "foo"
    rest   // []

如果將擴展運算符用於數組賦值，只能放在參數的最後一位，否則會報錯。

    const [...butLast, last] = [1, 2, 3, 4, 5];
    // 报错
    
    const [first, ...middle, last] = [1, 2, 3, 4, 5];
    // 报错
**（4）字串**
擴展運算符還可以將字符串轉為真正的數組。

    [...'hello']
    // [ "h", "e", "l", "l", "o" ]
**（5）Map 和Set 結構，Generator 函數**
擴展運算符內部調用的是數據結構的Iterator 接口，因此只要具有Iterator 接口的對象，都可以使用擴展運算符，比如Map 結構。

    let map = new Map([
      [1, 'one'],
      [2, 'two'],
      [3, 'three'],
    ]);
    
    let arr = [...map.keys()]; // [1, 2, 3]

### 🔅對象

    function f(x, y) {
      return {x, y};
    }
    
    // 等同于 
    function f(x, y) {
      return {x: x, y: y};
    }
    
    f(1, 2) //{x: 1, y: 2}
除了屬性簡寫，方法也可以簡寫。

    const o = {
      method() {
        return "Hello!";
      }
    };
    
    // 等同于
    const o = {
      method: function() {
        return "Hello!";
      }
    };
    
**屬性名表達式**
ES6 允許字面量定義對象時，用方法二（表達式）作為對象的屬性名，即把表達式放在方括號內。

    let propKey = 'foo';
    
    let obj = {
      [propKey]: true,
      ['a' + 'bc']: 123
    };

下面是另一個例子。

    let lastWord = 'last word';
    
    const a = {
      'first word': 'hello',
      [lastWord]: 'world'
    };
    
    a['first word'] // "hello"
    a[lastWord] // "world"
    a['last word'] // "world"

**super關鍵字**
指向當前對象的原型對象

    const proto = {
      foo: 'hello'
    };
    
    const obj = {
      foo: 'world',
      find() {
        return super.foo;
      }
    };
    
    Object.setPrototypeOf(obj, proto);
    //Object.setPrototypeOf(對象, 新原型);
    obj.find() // "hello"

上面代碼中，對象obj.find()方法之中，通過super.foo引用了原型對象proto的foo屬性。
注意，super關鍵字表示原型對象時，只能用在對象的方法之中，用在其他地方都會報錯。

    // 报错
    const obj = {
      foo: super.foo
    }
    
    // 报错
    const obj = {
      foo: () => super.foo
    }
    
    // 报错
    const obj = {
      foo: function () {
        return super.foo
      }
    }
JavaScript引擎內部，super.foo等同於`Object.getPrototypeOf(this).foo（屬性）`或`Object.getPrototypeOf(this).foo.call(this)（方法）`。

    const proto = {
      x: 'hello',
      foo() {
        console.log(this.x);
      },
    };
    
    const obj = {
      x: 'world',
      foo() {
        super.foo();   //console.log(this.x);
      }
    }
    
    Object.setPrototypeOf(obj, proto);
    
    obj.foo() // "world"

上面代碼中，super.foo指向原型對象proto的foo方法，但是綁定的this卻還是當前對象obj，因此輸出的就是world

**Object. is()**
ES5比較兩個值是否相等，只有兩個運算符：相等運算符`==`和嚴格相等運算符`===`。它們都有缺點，**前者會自動轉換數據類型，後者的NaN不等於自身，以及+0等於-0。**JavaScript缺乏一種運算，在所有環境中，只要兩個值是一樣的，它們就應該相等。
ES6提出“Same-value equality”（同值相等）算法，用來解決這個問題。Object.is就是部署這個算法的新方法。它用來比較兩個值是否嚴格相等，與嚴格比較運算符`===`的行為基本一致。

    Object.is('foo', 'foo')
    // true
    Object.is({}, {})
    // false
    不同之處只有兩

不同之處只有兩個：一是+0不等於-0，二是NaN等於自身。

    +0 === -0 //true
    NaN === NaN // false
    
    Object.is(+0, -0) // false
	Object.is(NaN, NaN) // true

**Object.assign（）**
Object.assign方法用於對象的合併，將源對象（source）的所有可枚舉屬性，複製到目標對象（target）。

    const target = { a: 1 };
    
    const source1 = { b: 2 };
    const source2 = { c: 3 };
    
    Object.assign(target, source1, source2);
    rce2);
	target // {a:1, b:2, c:3}
如果只有一個參數，Object.assign會直接返回該參數。

    const obj = {a: 1};
    Object.assign(obj) === obj // true
 如果該參數不是對象，則會先轉成對象，然後返回。

    typeof Object.assign(2) // "object"

由於undefined和null無法轉成對象，所以如果它們作為參數，就會報錯。

    Object.assign(undefined) // 报错
    Object.assign(null) // 报错   
    
  **Object.keys（）** 
  ES5引入了Object.keys方法，返回一個數組，成員是參數對象自身的（不含繼承的）所有可遍歷（enumerable）屬性的鍵名。

    var obj = { foo: 'bar', baz: 42 };
    Object.keys(obj)
    // ["foo", "baz"]

ES2017 引入了跟Object.keys配套的Object.values和Object.entries，作為遍歷一個對象的補充手段，供for...of循環使用。

    let {keys, values, entries} = Object;
    let obj = { a: 1, b: 2, c: 3 };
    
    for (let key of keys(obj)) {
      console.log(key); // 'a', 'b', 'c'
    }
    
    for (let value of values(obj)) {
      console.log(value); // 1, 2, 3
    }
    
    for (let [key, value] of entries(obj)) {
      console.log([key, value]); // ['a', 1], ['b', 2], ['c', 3]
    }
### 🔅Symbol
ES5的對象屬性名都是字符串，這容易造成屬性名的衝突。比如，你使用了一個他人提供的對象，但又想為這個對象添加新的方法（mixin模式），新方法的名字就有可能與現有方法產生衝突。如果有一種機制，保證每個屬性的名字都是獨一無二的就好了，這樣就從根本上防止屬性名的衝突。這就是ES6引入Symbol的原因

    let s = Symbol();
    typeof s    // "symbol"
Symbol函數可以接受一個字符串作為參數，表示對Symbol 實例的描述，主要是為了在控制台顯示，或者轉為字符串時，比較容易區分。

    let s1 = Symbol('foo');
    let s2 = Symbol('bar');
    
    s1 // Symbol(foo)
    s2 // Symbol(bar)
    
    s1.toString() // "Symbol(foo)"
    s2.toString() // "Symbol(bar)"
    String(s1)    // "Symbol(foo)"
    
如果Symbol的參數是一個對象，就會調用該對象的toString方法，將其轉為字符串，然後才生成一個Symbol值。

    const obj = {
      toString() {
        return 'abc';
      }
    };
    const sym = Symbol(obj);
    sym // Symbol(abc)
注意，Symbol函數的參數只是表示對當前Symbol值的描述，因此相同參數的Symbol函數的返回值是不相等的。

    // 没有参数的情况
    let s1 = Symbol();
    let s2 = Symbol();
    
    s1 === s2 // false
    
    // 有参数的情况
    let s1 = Symbol('foo');
    let s2 = Symbol('foo');
    
    s1 === s2 // false
Symbol 值不能與其他類型的值進行運算，會報錯。

    let sym = Symbol('My symbol');
    
    "your symbol is " + sym
    // TypeError: can't convert symbol to string
    `your symbol is ${sym}`
    // TypeError: can't convert symbol to string
Symbol 值也可以轉為布爾值，但是不能轉為數值。

    let sym = Symbol();
    Boolean(sym) // true
    !sym  // false
    
    if (sym) {
      // ...
    }
    
    Number(sym) // TypeError
    sym + 2 // TypeError

**作為屬性名的Symbol**
由於每一個Symbol 值都是不相等的，這意味著Symbol 值可以作為標識符，用於對象的屬性名，就能保證不會出現同名的屬性。這對於一個對象由多個模塊構成的情況非常有用，能防止某一個鍵被不小心改寫或覆蓋。

    let mySymbol = Symbol();
    
    // 第一种写法
    let a = {};
    a[mySymbol] = 'Hello!';
    
    // 第二种写法
    let a = {
      [mySymbol]: 'Hello!'
    };
    
    // 第三种写法
    let a = {};
    Object.defineProperty(a, mySymbol, { value: 'Hello!' });
    
    // 以上写法都得到同样结果
    a[mySymbol] // "Hello!"

上面代碼通過方括號結構和Object.defineProperty，將對象的屬性名指定為一個Symbol值。
Symbol 類型還可以用於定義一組常量，保證這組常量的值都是不相等的。

    const log = {};
    
    log.levels = {
      DEBUG: Symbol('debug'),
      INFO: Symbol('info'),
      WARN: Symbol('warn')
    };
    console.log(log.levels.DEBUG, 'debug message');
    console.log(log.levels.INFO, 'info message');
下面是另外一個例子。

    const COLOR_RED    = Symbol();
    const COLOR_GREEN  = Symbol();
    
    function getComplement(color) {
      switch (color) {
        case COLOR_RED:
          return COLOR_GREEN;
        case COLOR_GREEN:
          return COLOR_RED;
        default:
          throw new Error('Undefined color');
        }
    }

**屬性名的遍歷**
Symbol作為屬性名，該屬性不會出現在for...in、for...of循環中，也不會被Object.keys()、Object.getOwnPropertyNames()、JSON.stringify()返回。但是，它也不是私有屬性，有一個`Object.getOwnPropertySymbols`方法，可以獲取指定對象的所有Symbol屬性名。
**Object.getOwnPropertySymbols方法返回一個數組，成員是當前對象的所有用作屬性名的Symbol 值。**

    const obj = {};
    let a = Symbol('a');
    let b = Symbol('b');
    
    obj[a] = 'Hello';
    obj[b] = 'World';
    
    const objectSymbols = Object.getOwnPropertySymbols(obj);
    
    objectSymbols
    // [Symbol(a), Symbol(b)]
    
**Symbol.for（），Symbol.keyFor（）**
有時我們希望重新使用同一個Symbol值，Symbol.for方法可以做到這一點。它接受一個字符串作為參數，然後搜索有沒有以該參數作為名稱的Symbol值。如果有，就返回這個Symbol值，否則就新建並返回一個以該字符串為名稱的Symbol值

    let s1 = Symbol.for('foo');
    let s2 = Symbol.for('foo');
    
    s1 === s2 // true
Symbol.keyFor方法返回一個已登記的Symbol類型值的key。

    let s1 = Symbol.for("foo");
    Symbol.keyFor(s1) // "foo"
    
    let s2 = Symbol("foo");
    Symbol.keyFor(s2) // undefined

**實例:模塊的Singleton模式**
**mod.js**

    function A() {
      this.foo = 'hello';
    }
    
    if (!global._foo) {
      global._foo = new A();
    }
    
    module.exports = global._foo;
然後，加載上面的mod.js。

    const a = require('./mod.js');
    console.log(a.foo);
但是，這裡有一個問題，全局變量global._foo是可寫的，任何文件都可以修改。

    global._foo = { foo: 'world' };
    
    const a = require('./mod.js');
    console.log(a.foo);

上面的代碼，會使得加載mod.js的腳本都失真。
為了防止這種情況出現，我們就可以使用Symbol。
 **mod.js**

    const FOO_KEY = Symbol.for('foo');
    //如果鍵名使用Symbol方法生成，那麼外部將無法引用這個值，當然也就無法改寫。
    
    function A() {
      this.foo = 'hello';
    }
    
    if (!global[FOO_KEY]) {
      global[FOO_KEY] = new A();
    }
    
    module.exports = global[FOO_KEY];
上面代碼中，可以保證global[FOO_KEY]不會被無意間覆蓋，但還是可以被改寫。

    global[Symbol.for('foo')] = { foo: 'world' };
    const a = require('./mod.js');
[內置的Symbol值](http://es6.ruanyifeng.com/#docs/symbol#%E5%86%85%E7%BD%AE%E7%9A%84-Symbol-%E5%80%BC)

### 🔅Set 和Map 
[Set 和Map 數據結構](http://es6.ruanyifeng.com/#docs/set-map)
**Set**
它類似於數組，但是成員的值都是唯一的，沒有重複的值。
    const s = new Set();
    
    [2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));
    
    for (let i of s) {
      console.log(i);
    }
    // 2 3 5 4
Set函數可以接受一個數組（或者俱有iterable 接口的其他數據結構）作為參數，用來初始化。

    // 例一
    const set = new Set([1, 2, 3, 4, 4]);
    [...set]
    // [1, 2, 3, 4]
    
    // 例二
    const items = new Set([1, 2, 3, 4, 5, 5, 5, 5]);
    items.size // 5
    
    // 例三
    const set = new Set(document.querySelectorAll('div'));
    set.size // 56
    
    // 类似于
    const set = new Set();
    document
     .querySelectorAll('div')
     .forEach(div => set.add(div));
    set.size // 56
去除數組重複成員的方法

    // 去除数组的重复成员
    [...new Set(array)]

去除字串裡面的重複字符

    [...new Set('ababbc')].join('')
    // "abc"
在Set內部，兩個NaN是相等。

    let set = new Set();
    let a = NaN;
    let b = NaN;
    set.add(a);
    set.add(b);
    set // Set {NaN}

另外，兩個對象總是不相等的。

    let set = new Set();
    
    set.add({});
    set.size // 1
    
    set.add({});
    set.size // 2

**Set實例的屬性和方法** 
**Set 結構的實例有以下屬性:**

 - Set.prototype.constructor：構造函數，默認就是Set函數。
 - Set.prototype.size：返回Set實例的成員總數。

**Set 實例的方法分為兩大類：操作方法（用於操作數據）和遍歷方法（用於遍歷成員）。
下面先介紹四個操作方法。**
 - add(value)：添加某個值，返回Set 結構本身。
 - delete(value)：刪除某個值，返回一個布爾值，表示刪除是否成功。
 - has(value)：返回一個布爾值，表示該值是否為Set的成員。
 - clear()：清除所有成員，沒有返回值

**Set 結構的實例有四個遍歷方法，可以用於遍歷成員。**

 - keys()：返回鍵名的遍歷器 =values()
 - values()：返回鍵值的遍歷器 =keys()
 - entries()：返回鍵值對的遍歷器
 - forEach()：使用回調函數遍歷每個成員

實例:

    let set = new Set(['red', 'green', 'blue']);
    
    for (let item of set.keys()) {
      console.log(item);
    }
    // red
    // green
    // blue
    
    for (let item of set.values()) {
      console.log(item);
    }
    // red
    // green
    // blue
    
    for (let item of set.entries()) {
      console.log(item);
    }
    // ["red", "red"]
    // ["green", "green"]
    // ["blue", "blue"]
這意味著，可以省略values方法，直接用for...of循環遍歷Set。

    let set = new Set(['red', 'green', 'blue']);
    
    for (let x of set) {
      console.log(x);
    }
    // red
    // green
    // blue

Array.from方法可以將Set 結構轉為數組。

    const items = new Set([1, 2, 3, 4, 5]);
    const array = Array.from(items);

**Map**
對象（Object）本質上是鍵值對的集合（Hash 結構），但是傳統上只能用字符串當作鍵。這給它的使用帶來了很大的限制。
為了解決這個問題，ES6 提供了Map 數據結構。它類似於對象，**也是鍵值對的集合**，**但是“鍵”的範圍不限於字符串，各種類型的值（包括對象）都可以當作鍵**。也就是說，Object 結構提供了“字符串—值”的對應，Map 結構提供了“值—值”的對應，是一種更完善的Hash 結構實現。**如果你需要“鍵值對”的數據結構，Map 比Object 更合適。**

     // Map 基本使用
    let map = new Map();
    
    // 可以使用 set() 方法設置資料內容
    map.set('first', 1);
    map.set(10, 'ten');
    map.set({sayHi: 'Hi'}, 'obj');
    
    console.log(map);  
    // Map(3) {"first" => 1, 10 => "ten", {…} => "obj"}
**Map 也可以接受一個數組作為參數**。該數組的成員是一個個表示鍵值對的數組。

    const map = new Map([
      ['name', '张三'],
      ['title', 'Author']
    ]);
    
    map.size // 2
    map.has('name') // true
    map.get('name') // "张三"
    map.has('title') // true
    map.get('title') // "Author"
Map構造函數接受數組作為參數，實際上執行的是下面的算法。

    const items = [
      ['name', '张三'],
      ['title', 'Author']
    ];
    
    const map = new Map();
    
    items.forEach(
      ([key, value]) => map.set(key, value)
    );
不僅僅是數組，任何具有Iterator接口、且每個成員都是一個雙元素的數組的數據結構（詳見《Iterator》一章）都可以當作Map構造函數的參數。這就是說，Set和Map都可以用來生成新的Map。

    const set = new Set([
      ['foo', 1],
      ['bar', 2]
    ]);
    const m1 = new Map(set);
    m1.get('foo') // 1
    
    const m2 = new Map([['baz', 3]]);
    const m3 = new Map(m2);
    m3.get('baz') // 3
如果對同一個鍵多次賦值，後面的值將覆蓋前面的值。

    const map = new Map();
    
    map
    .set(1, 'aaa')
    .set(1, 'bbb');
    
    map.get(1) // "bbb"
同樣的值的兩個實例，在Map 結構中被視為兩個鍵。

    const map = new Map();
    
    const k1 = ['a'];
    const k2 = ['a'];
    
    map
    .set(k1, 111)
    .set(k2, 222);
    
    map.get(k1) // 111
    map.get(k2) // 222

**實例的屬性和操作方法**
**（1）.size**

    const map = new Map();
    map.set('foo', true);
    map.set('bar', false);

    map.size // 2

**（2）.set（key，value）**

    const m = new Map();
    
    m.set('edition', 6)        // 键是字符串
    m.set(262, 'standard')     // 键是数值
    m.set(undefined, 'nah')    // 键是 undefined

set方法返回的是當前的Map對象，因此可以採用鍊式寫法。
    
    let map = new Map()
      .set(1, 'a')
      .set(2, 'b')
      .set(3, 'c');

**（3）.get(key)**
get方法讀取key對應的鍵值，如果找不到key，返回undefined。

    const m = new Map();
    
    const hello = function() {console.log('hello');};
    m.set(hello, 'Hello ES6!') // 键是函数
    
    m.get(hello)  // Hello ES6!

**（4）.has(key)**

    const m = new Map();
    
    m.set('edition', 6);
    m.set(262, 'standard');
    m.set(undefined, 'nah');
    
    m.has('edition')     // true
    m.has('years')       // false
    m.has(262)           // true
    m.has(undefined)     // true

**（5）.delete(key)**
delete方法刪除某個鍵，返回true。如果刪除失敗，返回false。

    const m = new Map();
    m.set(undefined, 'nah');
    m.has(undefined)     // true
    
    m.delete(undefined)
    m.has(undefined)       // false

**（6）.clear()**

    let map = new Map();
    map.set('foo', true);
    map.set('bar', false);
    
    map.size // 2
    map.clear()
    map.size // 0

**遍歷方法**

 - keys()：返回鍵名的遍歷器。
 - values()：返回鍵值的遍歷器。
 - entries()：返回所有成員的遍歷器。
 - forEach()：遍歷Map 的所有成員。

需要特別注意的是，Map 的遍歷順序就是插入順序。

    const map = new Map([
      ['F', 'no'],
      ['T',  'yes'],
    ]);
    
    for (let key of map.keys()) {
      console.log(key);
    }
    // "F"
    // "T"
    
    for (let value of map.values()) {
      console.log(value);
    }
    // "no"
    // "yes"
    
    for (let item of map.entries()) {
      console.log(item[0], item[1]);
    }
    // "F" "no"
    // "T" "yes"
    
    // 或者
    for (let [key, value] of map.entries()) {
      console.log(key, value);
    }
    // "F" "no"
    // "T" "yes"
    
    // 等同于使用map.entries()
    for (let [key, value] of map) {
      console.log(key, value);
    }
    // "F" "no"
    // "T" "yes"

Map結構轉為數組結構，比較快速的方法是使用擴展運算符（...）。

    const map = new Map([
      [1, 'one'],
      [2, 'two'],
      [3, 'three'],
    ]);
    
    [...map.keys()]
    // [1, 2, 3]
    
    [...map.values()]
    // ['one', 'two', 'three']
    
    [...map.entries()]
    // [[1,'one'], [2, 'two'], [3, 'three']]
    
    [...map]
    // [[1,'one'], [2, 'two'], [3, 'three']]
結合數組的map方法、filter方法，可以實現Map的遍歷和過濾（Map本身沒有map和filter方法）。

    const map0 = new Map()
      .set(1, 'a')
      .set(2, 'b')
      .set(3, 'c');
    
    const map1 = new Map(
      [...map0].filter(([k, v]) => k < 3)
    );
    // 产生 Map 结构 {1 => 'a', 2 => 'b'}
    
    const map2 = new Map(
      [...map0].map(([k, v]) => [k * 2, '_' + v])
        );
    // 产生 Map 结构 {2 => '_a', 4 => '_b', 6 => '_c'}

**與其他數據結構的互相轉換**
**（1）Map 轉為數組**

    const myMap = new Map()
      .set(true, 7)
      .set({foo: 3}, ['abc']);
    [...myMap]
    // [ [ true, 7 ], [ { foo: 3 }, [ 'abc' ] ] ]

**（2）數組轉為Map**

    new Map([
      [true, 7],
      [{foo: 3}, ['abc']]
    ])
    // Map {
    //   true => 7,
    //   Object {foo: 3} => ['abc']
    // }

**（3）Map 轉為對象**
如果所有Map 的鍵都是字符串，它可以無損地轉為對象。

    function strMapToObj(strMap) {
      let obj = Object.create(null);
      for (let [k,v] of strMap) {
        obj[k] = v;
      }
      return obj;
    }
    
    const myMap = new Map()
      .set('yes', true)
      .set('no', false);
    strMapToObj(myMap)
    // { yes: true, no: false }

如果有非字符串的鍵名，那麼這個鍵名會被轉成字符串，再作為對象的鍵名。

**（4）對象轉為Map**

    function objToStrMap(obj) {
      let strMap = new Map();
      for (let k of Object.keys(obj)) {
        strMap.set(k, obj[k]);
      }
      return strMap;
    }
    
    objToStrMap({yes: true, no: false})
    // Map {"yes" => true, "no" => false}

**（5）Map 轉為JSON**
Map 轉為JSON 要區分兩種情況。一種情況是，Map 的鍵名都是字符串，這時可以選擇轉為對象JSON。

    function strMapToJson(strMap) {
      return JSON.stringify(strMapToObj(strMap));
    }
    
    let myMap = new Map().set('yes', true).set('no', false);
    strMapToJson(myMap)
    // '{"yes":true,"no":false}'

另一種情況是，Map 的鍵名有非字符串，這時可以選擇轉為數組JSON。

    function mapToArrayJson(map) {
      return JSON.stringify([...map]);
    }
    
    let myMap = new Map().set(true, 7).set({foo: 3}, ['abc']);
    mapToArrayJson(myMap)
    // '[[true,7],[{"foo":3},["abc"]]]'

**（6）JSON 轉為Map**
JSON 轉為Map，正常情況下，所有鍵名都是字符串。

    function jsonToStrMap(jsonStr) {
      return objToStrMap(JSON.parse(jsonStr));
    }
    
    jsonToStrMap('{"yes": true, "no": false}')
    // Map {'yes' => true, 'no' => false}

但是，有一種特殊情況，整個JSON 就是一個數組，且每個數組成員本身，又是一個有兩個成員的數組。這時，它可以一一對應地轉為Map。這往往是Map 轉為數組JSON 的逆操作。

    function jsonToMap(jsonStr) {
      return new Map(JSON.parse(jsonStr));
    }
    
    jsonToMap('[[true,7],[{"foo":3},["abc"]]]')
    // Map {true => 7, Object {foo: 3} => ['abc']}

### 🔅Promise
[JavaScript ES6 Promise](https://wcc723.github.io/life/2017/05/25/promise/)
Promise是異步編程的一種解決方案，比傳統的解決方案——回調函數和事件——更合理和更強大。

所謂Promise，簡單說就是一個容器，裡面保存著某個未來才會結束的事件（通常是一個異步操作）的結果。從語法上說，Promise是一個對象，從它可以獲取異步操作的消息。Promise提供統一的API，各種異步操作都可以用同樣的方法進行處理。

Promise對像有以下兩個特點
（1）對象的狀態不受外界影響。Promise對象代表一個異步操作，有三種狀態：**pending（進行中）、fulfilled（已成功）和rejected（已失敗）**。只有異步操作的結果，可以決定當前是哪一種狀態，任何其他操作都無法改變這個狀態。這也是Promise這個名字的由來，它的英語意思就是“承諾”，表示其他手段無法改變。

（2）一旦狀態改變，就不會再變，任何時候都可以得到這個結果。Promise對象的狀態改變，只有兩種可能：從pending變為fulfilled和從pending變為rejected。只要這兩種情況發生，狀態就凝固了，不會再變了，會一直保持這個結果，這時就稱為resolved（已定型）。如果改變已經發生了，你再對Promise對象添加回調函數，也會立即得到這個結果。這與事件（Event）完全不同，事件的特點是，如果你錯過了它，再去監聽，是得不到結果的。
有了Promise對象，就可以將異步操作以同步操作的流程表達出來，避免了層層嵌套的回調函數。此外，Promise對象提供統一的接口，使得控制異步操作更加容易。
Promise也有一些缺點。
首先，**無法取消Promise，一旦新建它就會立即執行，無法中途取消**。
其次，**如果不設置回調函數，Promise內部拋出的錯誤，不會反應到外部**。
第三，當處於pending狀態時，無法得知目前進展到哪一個階段（剛剛開始還是即將完成）。

**如果某些事件不斷地反復發生，一般來說，使用Stream模式是比部署Promise更好的選擇。**

    const promise = new Promise(function(resolve, reject) {
      // ... some code
    
      if (/* 异步操作成功 */){
        resolve(value);
      } else {
        reject(error);
      }
    });
Promise構造函數接受一個函數作為參數，該函數的兩個參數分別是resolve和reject。它們是兩個函數，由JavaScript引擎提供，不用自己部署。
resolve函數的作用是，將Promise對象的狀態從“未完成”變為“成功”（即從pending變為resolved），在異步操作成功時調用，並將異步操作的結果，作為參數傳遞出去；reject函數的作用是，將Promise對象的狀態從“未完成”變為“失敗”（即從pending變為rejected），在異步操作失敗時調用，並將異步操作報出的錯誤，作為參數傳遞出去。

Promise實例生成以後，可以用then方法分別指定resolved狀態和rejected狀態的回調函數。

    promise.then(function(value) {
      // success
    }, function(error) {
      // failure
    });

下面是一個Promise對象的簡單例子。

    function timeout(ms) {
      return new Promise((resolve, reject) => {
        setTimeout(resolve, ms, 'done');
      });
    }
    
    timeout(100).then((value) => {
      console.log(value);   //0.1秒後印出done
    });

上面代碼中，timeout方法返回一個Promise實例，表示一段時間以後才會發生的結果。過了指定的時間（ms參數）以後，Promise實例的狀態變為resolved，就會觸發then方法綁定的回調函數。
Promise 新建後就會立即執行。

    let promise = new Promise(function(resolve, reject) {
      console.log('Promise');
      resolve();
    });
    
    promise.then(function() {
      console.log('resolved.');
    });
    
    console.log('Hi!');
    
    // Promise
    // Hi!
    // resolved
上面代碼中，Promise新建後立即執行，所以首先輸出的是Promise。然後，then方法指定的回調函數，將在當前腳本**所有同步任務執行完才會執行**，所以resolved最後輸出。

下面是異步加載圖片的例子。

    function loadImageAsync(url) {
      return new Promise(function(resolve, reject) {
        const image = new Image();
    
        image.onload = function() {
          resolve(image);
        };
    
        image.onerror = function() {
          reject(new Error('Could not load image at ' + url));
        };
    
        image.src = url;
      });
    }
上面代碼中，使用Promise包裝了一個圖片加載的異步操作。如果加載成功，就調用resolve方法，否則就調用reject方法。

下面是一個用Promise對象實現的Ajax操作的例子。

    const getJSON = function(url) {
      const promise = new Promise(function(resolve, reject){
        const handler = function() {
          if (this.readyState !== 4) {
            return;
          }
          if (this.status === 200) {
            resolve(this.response);
          } else {
            reject(new Error(this.statusText));
          }
        };
        const client = new XMLHttpRequest();
        client.open("GET", url);
        client.onreadystatechange = handler;
        client.responseType = "json";
        client.setRequestHeader("Accept", "application/json");
        client.send();
    
      });
    
      return promise;
    };
    
    getJSON("/posts.json").then(function(json) {
      console.log('Contents: ' + json);
    }, function(error) {
      console.error('出错了', error);
    });

上面代碼中，getJSON是對XMLHttpRequest對象的封裝，用於發出一個針對JSON數據的HTTP請求，並且返回一個Promise對象。需要注意的是，在getJSON內部，resolve函數和reject函數調用時，都帶有參數。

    const p1 = new Promise(function (resolve, reject) {
      setTimeout(() => reject(new Error('fail')), 3000)
    })
    
    const p2 = new Promise(function (resolve, reject) {
      setTimeout(() => resolve(p1), 1000)
    })
    
    p2
      .then(result => console.log(result))
      .catch(error => console.log(error))
    // 三秒後Error: fail

上面代碼中，p1是一個Promise，3秒之後變為rejected。p2的狀態在1秒之後改變，resolve方法返回的是p1。由於p2返回的是另一個Promise，導致p2自己的狀態無效了，由p1的狀態決定p2的狀態。所以，後面的then語句都變成針對後者（p1）。又過了2秒，p1變為rejected，導致觸發catch方法指定的回調函數。
一般來說，調用resolve或reject以後，Promise的使命就完成了，後繼操作應該放到then方法裡面，而不應該直接寫在resolve或reject的後面。

    new Promise((resolve, reject) => {
      return resolve(1);
      // 后面的语句不会执行
      console.log(2);
    })
    
**Promise.prototype.catch（）**
Promise.prototype.catch方法是.then(null, rejection)或.then(undefined, rejection)的別名，用於指定發生錯誤時的回調函數。

    getJSON('/posts.json').then(function(posts) {
      // ...
    }).catch(function(error) {
      // 处理 getJSON 和 前一个回调函数运行时发生的错误
      console.log('发生错误！', error);
    });
下面是一個例子。

    const promise = new Promise(function(resolve, reject) {
      throw new Error('test');
    });
    promise.catch(function(error) {
      console.log(error);
    });
    // Error: test

一般來說，不要在then方法裡面定義Reject狀態的回調函數（即then的第二個參數），總是使用catch方法。

    // bad
    promise
      .then(function(data) {
        // success
      }, function(err) {
        // error
      });
    
    // good
    promise
      .then(function(data) { //cb
        // success
      })
      .catch(function(err) {
        // error
      });

上面代碼中，第二種寫法要好於第一種寫法，理由是第二種寫法可以捕獲前面then方法執行中的錯誤，也更接近同步的寫法（try/catch）。因此，建議總是使用catch方法，而不使用then方法的第二個參數。

**Promise.prototype.finally（）**
finally方法用於指定不管Promise 對象最後狀態如何，都會執行的操作。該方法是ES2018 引入標準的。

    promise
    .then(result => {···})
    .catch(error => {···})
    .finally(() => {···});
    
**Promise.all（）**
Promise.all方法用於將多個Promise 實例，包裝成一個新的Promise 實例。

    const p = Promise.all([p1, p2, p3]);
p的狀態由p1、p2、p3決定，分成兩種情況。
1. 只有p1、p2、p3的狀態都變成fulfilled，p的狀態才會變成fulfilled，此時p1、p2、p3的返回值組成一個數組，傳遞給p的回調函數。
2. 只要p1、p2、p3之中有一個被rejected，p的狀態就變成rejected，此時第一個被reject的實例的返回值，會傳遞給p的回調函數。

下面是一個具體的例子。

    // 生成一个Promise对象的数组
    const promises = [2, 3, 5, 7, 11, 13].map(function (id) {
      return getJSON('/post/' + id + ".json");
    });
    
    Promise.all(promises).then(function (posts) {
      // ...
    }).catch(function(reason){
      // ...
    });

上面代碼中，promises是包含6個Promise實例的數組，只有這6個實例的狀態都變成fulfilled，或者其中有一個變為rejected，才會調用Promise.all方法後面的回調函數。
注意，如果作為參數的Promise實例，自己定義了catch方法，那麼它一旦被rejected，並不會觸發Promise.all()的catch方法。

    const p1 = new Promise((resolve, reject) => {
      resolve('hello');
    })
    .then(result => result)
    .catch(e => e);
    
    const p2 = new Promise((resolve, reject) => {
      throw new Error('报错了');
    })
    .then(result => result)
    .catch(e => e);
    
    Promise.all([p1, p2])
    .then(result => console.log(result))
    .catch(e => console.log(e));
    // ["hello", Error: 报错了]

如果p2沒有自己的catch方法，就會調用Promise.all()的catch方法。

    const p1 = new Promise((resolve, reject) => {
      resolve('hello');
    })
    .then(result => result);
    
    const p2 = new Promise((resolve, reject) => {
      throw new Error('报错了');
    })
    .then(result => result);
    
    Promise.all([p1, p2])
    .then(result => console.log(result))
    .catch(e => console.log(e));
    // Error: 报错了

**Promise.race（）**

    const p = Promise.race([p1, p2, p3]);

上面代碼中，只要p1、p2、p3之中有一個實例率先改變狀態，p的狀態就跟著改變。那個率先改變的Promise實例的返回值，就傳遞給p的回調函數。

**應用**
**加載圖片**
我們可以將圖片的加載寫成一個Promise，一旦加載完成，Promise的狀態就發生變化。

    const preloadImage = function (path) {
      return new Promise(function (resolve, reject) {
        const image = new Image();
        image.onload  = resolve;
        image.onerror = reject;
        image.src = path;
      });
    };

### 🔅Generator 
形式上，Generator函數是一個普通函數，但是有兩個特徵。
一是，function關鍵字與函數名之間有一個星號；
二是，函數體內部使用yield表達式(只能在Generator中)，定義不同的內部狀態

    function* helloWorldGenerator() {
      yield 'hello';
      yield 'world';
      return 'ending';
    }
    
    var hw = helloWorldGenerator();

上面代碼定義了一個Generator函數helloWorldGenerator，它內部有兩個yield表達式（hello和world），即該函數有三個狀態：hello，world和return語句（結束執行）。

下一步，必須調用遍歷器對象的next方法，使得指針移向下一個狀態。也就是說，每次調用next方法，內部指針就從函數頭部或上一次停下來的地方開始執行，直到遇到下一個yield表達式（或return語句）為止。換言之，Generator函數是分段執行的，yield表達式是暫停執行的標記，而next方法可以恢復執行。

    hw.next()
    // { value: 'hello', done: false }
    
    hw.next()
    // { value: 'world', done: false }
    
    hw.next()
    // { value: 'ending', done: true }
    
    hw.next()
    // { value: undefined, done: true }
ES6沒有規定，function關鍵字與函數名之間的星號，寫在哪個位置。這導致下面的寫法都能通過。

    function * foo(x, y) { ··· }
    function *foo(x, y) { ··· }
    function* foo(x, y) { ··· }
    function*foo(x, y) { ··· }

Generator函數可以不用yield表達式，這時就變成了一個單純的暫緩執行函數。

    function* f() {
      console.log('执行了！')
    }
    
    var generator = f();
    
    setTimeout(function () {
      generator.next()
    }, 2000);

yield表達式如果用在另一個表達式之中，必須放在圓括號裡面。

    function* demo() {
      console.log('Hello' + yield); // SyntaxError
      console.log('Hello' + yield 123); // SyntaxError
    
      console.log('Hello' + (yield)); // OK
      console.log('Hello' + (yield 123)); // OK
    }

yield表達式用作函數參數或放在賦值表達式的右邊，可以不加括號。

    function* demo() {
      foo(yield 'a', yield 'b'); // OK
      let input = yield; // OK
    }
**與Iterator接口的關係**
由於Generator函數就是遍歷器生成函數，因此可以把Generator賦值給對象的Symbol.iterator屬性，從而使得該對象具有Iterator接口。

    var myIterable = {};
    myIterable[Symbol.iterator] = function* () {
      yield 1;
      yield 2;
      yield 3;
    };
    
    [...myIterable] // [1, 2, 3]

**next方法的參數**
next方法可以帶一個參數，該參數就會被當作上一個yield表達式的返回值。

    function* foo(x) {
      var y = 2 * (yield (x + 1));
      var z = yield (y / 3);
      return (x + y + z);
    }
    
    var a = foo(5);
    a.next() // Object{value:6, done:false}
    a.next() // Object{value:NaN, done:false}
    a.next() // Object{value:NaN, done:true}
    
    var b = foo(5);
    b.next() // { value:6, done:false }
    b.next(12) // { value:8, done:false }
    b.next(13) // { value:42, done:true }

上面代碼中，第二次運行next方法的時候不帶參數，導致y的值等於2 * undefined（即NaN），除以3以後還是NaN，因此返回對象的value屬性也等於NaN。第三次運行Next方法的時候不帶參數，所以z等於undefined，返回對象的value屬性等於5 + NaN + undefined，即NaN。

如果向next方法提供參數，返回結果就完全不一樣了。上面代碼第一次調用b的next方法時，返回x+1的值6；第二次調用next方法，將上一次yield表達式的值設為12，因此y等於24，返回y / 3的值8；第三次調用next方法，將上一次yield表達式的值設為13，因此z等於13，這時x等於5，y等於24，所以return語句的值等於42。

再看一個通過next方法的參數，向Generator函數內部輸入值的例子。

    function* dataConsumer() {
      console.log('Started');
      console.log(`1. ${yield}`);
      console.log(`2. ${yield}`);
      return 'result';
    }
    
    let genObj = dataConsumer();
    genObj.next();
    // Started
    genObj.next('a')
    // 1. a
    genObj.next('b')
    // 2. b

**for...of循環**
for...of循環可以自動遍歷Generator函數運行時生成的Iterator對象，且此時不再需要調用next方法。

    function* foo() {
      yield 1;
      yield 2;
      yield 3;
      yield 4;
      yield 5;
      return 6;
    }
    
    for (let v of foo()) {
      console.log(v);
    }
    // 1 2 3 4 5
下面是一個利用Generator函數和for...of循環，實現斐波那契數列的例子。

    function* fibonacci() {
      let [prev, curr] = [0, 1];
      for (;;) {
        yield curr;
        [prev, curr] = [curr, prev + curr];
      }
    }
    
    for (let n of fibonacci()) {
      if (n > 1000) break;
      console.log(n);
    }
    //1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987

除了for...of循環以外，擴展運算符（...）、解構賦值和Array.from方法內部調用的，都是遍歷器接口。這意味著，它們都可以將Generator函數返回的Iterator對象，作為參數。

    function* numbers () {
      yield 1
      yield 2
      return 3
      yield 4
    }
    
    // 扩展运算符
    [...numbers()] // [1, 2]
    
    // Array.from 方法
    Array.from(numbers()) // [1, 2]
    
    // 解构赋值
    let [x, y] = numbers();
    x // 1
    y // 2
    
    // for...of 循环
    for (let n of numbers()) {
      console.log(n)
    }
    // 1
    // 2

**yield*表達式**
如果在Generator 函數內部，調用另一個Generator 函數，默認情況下是沒有效果的。

    function* foo() {
      yield 'a';
      yield 'b';
    }
    
    function* bar() {
      yield 'x';
      foo();
      yield 'y';
    }
    
    for (let v of bar()){
      console.log(v);
    }
    // "x"
    // "y"

這個就需要用到yield*表達式，用來在一個Generator函數里面執行另一個Generator函數。

    function* bar() {
      yield 'x';
      yield* foo();
      yield 'y';
    }
    
    // 等同于
    function* bar() {
      yield 'x';
      yield 'a';
      yield 'b';
      yield 'y';
    }
    
    // 等同于
    function* bar() {
      yield 'x';
      for (let v of foo()) {
        yield v;
      }
      yield 'y';
    }
    
    for (let v of bar()){
      console.log(v);
    }
    // "x"
    // "a"
    // "b"
    // "y"

**Generator 函數的異步應用**
[Generator 函數的異步應用](http://es6.ruanyifeng.com/#docs/generator-async)

### 🔅async 函數
[從 ES6 Promise 到 ES7 的 async await 入門](https://mlwmlw.org/2016/03/es7-%E7%9A%84-async-await-%E5%85%A5%E9%96%80/comment-page-1/)
[同步與非同步](https://ithelp.ithome.com.tw/articles/10194569)

 - **非同步 (Asynchronous)**

但你不需要等到青菜切完才能處理番茄。
而是在收到食材的同時，負責青菜的朋友就去處理青菜，負責番茄的朋友就去處理番茄。

 - **同步(Synchronous)**

必須先弄完一項之後再去處理另一項，整個流程會被前一個步驟卡住。像這樣「先完成 A 才能做 B、C、D ...」

    const asyncRunFail = async () => {
      let mingRun = await runPromise('小明', 2000, false);
      let auntieRun = await runPromise('漂亮阿姨', 2500);
      return `${mingRun}, ${auntieRun}`
    }
    asyncRunFail().then(string => {
      console.log(string);
    }).catch(response => {
      console.log(response);
      // 小明 跌倒失敗(rejected)
    })

async函數對Generator 函數的改進，體現在以下四點。
**（1）內置執行器**
Generator函數的執行必須靠執行器，所以才有了co模塊，而async函數自帶執行器。也就是說，async函數的執行，與普通函數一模一樣，只要一行。

    asyncReadFile();

上面的代碼調用了asyncReadFile函數，然後它就會自動執行，輸出最後結果。這完全不像Generator函數，需要調用next方法，或者用co模塊，才能真正執行，得到最後結果。

**（2）更好的語義**
async和await，比起星號和yield，語義更清楚了。async表示函數里有異步操作，await表示緊跟在後面的表達式需要等待結果。

**（3）更廣的適用性**
co模塊約定，yield命令後面只能是Thunk函數或Promise對象，而async函數的await命令後面，可以是Promise對象和原始類型的值（數值、字符串和布爾值，但這時會自動轉成立即resolved的Promise對象）。

**（4）返回值是Promise**
async函數的返回值是Promise對象，這比Generator函數的返回值是Iterator對象方便多了。你可以用then方法指定下一步的操作。
進一步說，async函數完全可以看作多個異步操作，包裝成的一個Promise對象，而await命令就是內部then命令的語法糖。

**基本用法**

    async function getStockPriceByName(name) {
      const symbol = await getStockSymbol(name);
      const stockPrice = await getStockPrice(symbol);
      return stockPrice;
    }
    
    getStockPriceByName('goog').then(function (result) {
      console.log(result);
    });
下面是另一個例子，指定多少毫秒後輸出一個值。

    function timeout(ms) {
      return new Promise((resolve) => {
        setTimeout(resolve, ms);
      });
    }
    
    async function asyncPrint(value, ms) {
      await timeout(ms);
      console.log(value);
    }
    
    asyncPrint('hello world', 50);
上面代碼指定50毫秒以後，輸出hello world。
由於async函數返回的是Promise對象，可以作為await命令的參數。所以，上面的例子也可以寫成下面的形式。

    async function timeout(ms) {
      await new Promise((resolve) => {
        setTimeout(resolve, ms);
      });
    }
    
    async function asyncPrint(value, ms) {
      await timeout(ms);
      console.log(value);
    }
    
    asyncPrint('hello world', 50);

### 🔅Class

    //定义类
    class Point {
    
      constructor(x, y) {
        this.x = x;
        this.y = y;
      }
    
      toString() {
        return '(' + this.x + ', ' + this.y + ')';
      }
    
    }
    
    var point = new Point(2, 3);
    
    point.toString() // (2, 3)
    
    point.hasOwnProperty('x') // true
    point.hasOwnProperty('y') // true
    point.hasOwnProperty('toString') // false
    point.__proto__.hasOwnProperty('toString') // true

### 🔅繼承

    class Point {
    }
    
    class ColorPoint extends Point {
    }
constructor方法和toString方法之中，都出現了super關鍵字，它在這裡表示父類的構造函數，用來新建父類的this對象

    class ColorPoint extends Point {
      constructor(x, y, color) {
        super(x, y); // 调用父类的constructor(x, y)
        this.color = color;
      }
    
      toString() {
        return this.color + ' ' + super.toString(); // 调用父类的toString()
      }
    }
子類必須在constructor方法中調用super方法，否則新建實例時會報錯。這是因為子類自己的this對象，必須先通過父類的構造函數完成塑造，得到與父類同樣的實例屬性和方法，然後再對其進行加工，加上子類自己的實例屬性和方法。如果不調用super方法，子類就得不到this對象。

    class Point { /* ... */ }
    
    class ColorPoint extends Point {
      constructor() {
      }
    }
    
    let cp = new ColorPoint(); // ReferenceError

上面代碼中，ColorPoint繼承了父類Point，但是它的構造函數沒有調用super方法，導致新建實例時報錯。

    class A {
      constructor() {
        console.log(new.target.name);
      }
    }
    class B extends A {
      constructor() {
        super();
      }
    }
    new A() // A
    new B() // B

### 🔅Module
CommonJS 模塊就是對象，輸入時必須查找對象屬性。

    // CommonJS模块
    let { stat, exists, readFile } = require('fs');
    
    // 等同于
    let _fs = require('fs');
    let stat = _fs.stat;
    let exists = _fs.exists;
    let readfile = _fs.readfile;
上面代碼的實質是整體加載fs模塊（即加載fs的所有方法），生成一個對象（_fs），然後再從這個對像上面讀取3個方法。這種加載稱為“運行時加載”，因為只有運行時才能得到這個對象，導致完全沒辦法在編譯時做“靜態優化”。

ES6模塊不是對象，而是通過export命令顯式指定輸出的代碼，再通過import命令輸入。

    // ES6模块
    import { stat, exists, readFile } from 'fs';
上面代碼的實質是從`fs`模塊加載3個方法，其他方法不加載。這種加載稱為“編譯時加載”或者靜態加載，即ES6可以在編譯時就完成模塊加載，效率要比CommonJS模塊的加載方式高。當然，這也導致了沒法引用ES6模塊本身，因為它不是對象。

**export命令**

    // profile.js
    export var firstName = 'Michael';
    export var lastName = 'Jackson';
    export var year = 1958;
export的寫法，除了像上面這樣，還有另外一種。

    // profile.js
    var firstName = 'Michael';
    var lastName = 'Jackson';
    var year = 1958;
    
    export {firstName, lastName, year};
export命令除了輸出變量，還可以輸出函數或類（class）。

    export function multiply(x, y) {
      return x * y;
    };

通常情況下，export輸出的變量就是本來的名字，但是可以使用as關鍵字重命名。

    function v1() { ... }
    function v2() { ... }
    
    export {
      v1 as streamV1,
      v2 as streamV2,
      v2 as streamLatestVersion
    };
export命令規定的是對外的接口，必須與模塊內部的變量建立一一對應關係。

    // 报错
    export 1;
    
    // 报错
    var m = 1;
    export m;

上面兩種寫法都會報錯，因為沒有提供對外的接口。第一種寫法直接輸出1，第二種寫法通過變量m，還是直接輸出1。1只是一個值，不是接口。正確的寫法是下面這樣。

    // 写法一
    export var m = 1;
    
    // 写法二
    var m = 1;
    export {m};
    
    // 写法三
    var n = 1;
    export {n as m};
   

     // 报错
    function f() {}
    export f;
    
    // 正确
    export function f() {};
    
    // 正确
    function f() {}
    export {f};

**import命令**

    // main.js
    import {firstName, lastName, year} from './profile.js';
    
    function setName(element) {
      element.textContent = firstName + ' ' + lastName;
    }
如果想為輸入的變量重新取一個名字，import命令要使用as關鍵字，將輸入的變量重命名。

    import { lastName as surname } from './profile.js';
import命令輸入的變量都是只讀的，因為它的本質是輸入接口。也就是說，不允許在加載模塊的腳本里面，改寫接口。

    import {a} from './xxx.js'
    
    a = {}; // Syntax Error : 'a' is read-only;

**模塊的整體加載**
**circle.js**

    export function area(radius) {
      return Math.PI * radius * radius;
    }
    
    export function circumference(radius) {
      return 2 * Math.PI * radius;
    }
**main.js**

    import { area, circumference } from './circle';
    
    console.log('圆面积：' + area(4));
    console.log('圆周长：' + circumference(14));
上面寫法是逐一指定要加載的方法，整體加載的寫法如下。

    import * as circle from './circle';
    
    console.log('圆面积：' + circle.area(4));
    console.log('圆周长：' + circle.circumference(14));

**跨模塊常量**
如果要使用的常量非常多，可以建一個專門的constants目錄，將各種常量寫在不同的文件裡面，保存在該目錄下。

    // constants/db.js
    export const db = {
      url: 'http://my.couchdbserver.local:5984',
      admin_username: 'admin',
      admin_password: 'admin password'
    };
    
    // constants/user.js
    export const users = ['root', 'admin', 'staff', 'ceo', 'chief', 'moderator'];
然後，將這些文件輸出的常量，合併在index.js裡面。

    // constants/index.js
    export {db} from './db';
    export {users} from './users';

使用的時候，直接加載index.js就可以了。

    // script.js
    import {db, users} from './constants/index';

**inport()**
如果import命令要取代Node的require方法，這就形成了一個障礙。因為require是運行時加載模塊，import命令無法取代require的動態加載功能。

    const path = './' + fileName;
    const myModual = require(path);
上面的語句就是動態加載，require到底加載哪一個模塊，只有運行時才知道。import命令做不到這一點。
因此，有一個提案，建議引入import()函數，完成動態加載。

    import(specifier)

上面代碼中，import函數的參數specifier，指定所要加載的模塊的位置。import命令能夠接受什麼參數，import()函數就能接受什麼參數，兩者區別主要是後者為動態加載。

**import()類似於Node的require方法，區別主要是前者是異步加載，後者是同步加載。**

**適用場合**
**（1）按需加載。**

    button.addEventListener('click', event => {
      import('./dialogBox.js')
      .then(dialogBox => {
        dialogBox.open();
      })
      .catch(error => {
        /* Error handling */
      })
    });

上面代碼中，import()方法放在click事件的監聽函數之中，只有用戶點擊了按鈕，才會加載這個模塊。

**（2）條件加載**
import()可以放在if代碼塊，根據不同的情況，加載不同的模塊。

    if (condition) {
      import('moduleA').then(...);
    } else {
      import('moduleB').then(...);
    }

上面代碼中，如果滿足條件，就加載模塊A，否則加載模塊B。

### 🔅Module 的加載實現
**傳統方法**

    <!-- 页面内嵌的脚本 -->
    <script type="application/javascript">
      // module code
    </script>
    
    <!-- 外部脚本 -->
    <script type="application/javascript" src="path/to/myModule.js">
    </script>

上面代碼中，由於瀏覽器腳本的默認語言是JavaScript，因此type="application/javascript"可以省略。
默認情況下，瀏覽器是同步加載JavaScript腳本，即渲染引擎遇到<script>標籤就會停下來，等到執行完腳本，再繼續向下渲染。如果是外部腳本，還必須加入腳本下載的時間。

如果腳本體積很大，下載和執行的時間就會很長，因此造成瀏覽器堵塞，用戶會感覺到瀏覽器“卡死”了，沒有任何響應。這顯然是很不好的體驗，所以瀏覽器允許腳本異步加載，下面就是兩種異步加載的語法。

    <script src="path/to/myModule.js" defer></script>
    <script src="path/to/myModule.js" async></script>

defer與async的區別是：

 - defer要等到整個頁面在內存中正常渲染結束才會執行；
 - async一旦下載完，渲染引擎就會中斷渲染，執行這個腳本以後，再繼續渲染。

一句話，defer是“渲染完再執行”，async是“下載完就執行”。另外，如果有多個defer腳本，會按照它們在頁面出現的順序加載，而多個async腳本是不能保證加載順序的。

**加載規則**
瀏覽器加載ES6模塊，也使用`<script>`標籤，但是要加入**type="module"**屬性。

    <script type="module" src="./foo.js"></script>
    
瀏覽器對於帶有type="module"的`<script>`，都是異步加載，不會造成堵塞瀏覽器，即等到整個頁面渲染完，再執行模塊腳本，等同於打開了`<script>`標籤的defer屬性。

**Node加載**
目前，Node的import命令只支持加載本地模塊（file:協議），不支持加載遠程模塊。
如果模塊名不含路徑，那麼import命令會去**node_modules**目錄尋找這個模塊。

    import 'baz';
    import 'abc/123';

如果模塊名包含路徑，那麼import命令會按照路徑去尋找這個名字的腳本文件。

    import 'file:///etc/config/app.json';
    import './foo';
    import './foo?search';
    import '../bar';
    import '/baz';

如果腳本文件省略了後綴名，比如import './foo'，Node會依次嘗試四個後綴名：./foo.mjs、./foo.js、./foo.json、./foo.node。如果這些腳本文件都不存在，Node就會去加載./foo/package.json的main字段指定的腳本。如果./foo/package.json不存在或者沒有main字段，那麼就會依次加載./foo/index.mjs、./foo/index.js、./foo/index.json、./foo/index.node。如果以上四個文件還是都不存在，就會拋出錯誤。
最後，Node的import命令是異步加載，這一點與瀏覽器的處理方法相同。

**ES6模塊加載CommonJS模塊**
CommonJS模塊的輸出都定義在module.exports這個屬性上面。Node的import命令加載CommonJS模塊，Node會自動將module.exports屬性，當作模塊的默認輸出，即等同於export default xxx。

下面是一個CommonJS 模塊。

    // a.js
    module.exports = {
      foo: 'hello',
      bar: 'world'
    };
    
    // 等同于
    export default {
      foo: 'hello',
      bar: 'world'
    };
拿到CommonJS模塊的module.exports
 
    import baz from './a';
    // baz = {foo: 'hello', bar: 'world'};

bar本身是一個對象，不能當作函數調用，只能通過bar.default調用。

    // c.js
    module.exports = function two() {
      return 2;
    };
    
    // es.js
    import foo from './c';
    foo(); // 2
    
    import * as bar from './c';
    bar.default(); // 2
    bar(); // throws, bar is not a function
