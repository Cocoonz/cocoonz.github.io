# JavaScript忍者秘籍（第二版） 读书笔记

## 2 页面构建过程

![img](https://dpzbhybb2pdcj.cloudfront.net/maras/Figures/02fig01_alt.jpg)



### 页面构建

页面构建阶段的目标是建立Web应用的UI，主要包括两个步骤：

1. **解析HTML代码并构建文档对象模型（DOM）；**

   DOM是HTML元素的结构化表示

   ![img](https://dpzbhybb2pdcj.cloudfront.net/maras/Figures/02fig04_alt.jpg)

   > HTML规范：[https://html.spec.whatwg.org][https://html.spec.whatwg.org]<br />
   >
   > DOM规范：[https://dom.spec.whatwg.org][https://dom.spec.whatwg.org]

2. **执行JavaScript代码。**

   由浏览器JavaScript引擎执行。

   浏览器通过全局对象提供API，使JavaScript引擎可以与之交互并改变页面内容，其中最主要的是window对象，它代表了包含着一个页面的窗口，是获取其他所有全局对象、全局变量和浏览器API的访问途径，window对象中最重要的属性是document。

   代码分为全局代码和函数代码，全局代码由JavaScript引擎自动执行，函数代码必须被其他代码（可以是其他函数，也可以是浏览器）调用才能执行。

   **所有在某个JavaScript代码执行期间用户创建的全局变量都能正常地被其他脚本元素中的JavaScript代码所访问到。原因是全局window对象会存在于整个页面的生命周期内，它存储着所有JavaScript变量。**

在解析HTML代码时遇到脚本元素（script）则停止HTML到DOM的转建，开始执行JavaScript代码。

> 浏览器API参考文档：[https://developer.mozilla.org/en-US/docs/Web/API][https://developer.mozilla.org/en-US/docs/Web/API]



### 事件处理

浏览器核心思想：单线程执行模型（同一时刻只能执行一个代码片段）。

处理顺序是事件生成的顺序。

事件队列：跟踪已经发生但尚未处理的事件。

事件是异步的。

##### 事件类型

1. 浏览器事件，例如当页面加载完成后或无法加载时；
2. 网络事件，例如来自服务器的响应（Ajax事件和服务器端事件）；
3. 用户事件，例如鼠标单击、鼠标移动和键盘事件；
4. 计时器事件，当timeout时间到期或又触发了一次时间间隔。

事件处理器：某个特定事件发生后我们希望执行的函数。

##### 注册事件处理器的方式

1. 通过把函数赋给某个特殊属性；

   ```javascript
   window.onload = function(); //将一个函数赋值给window对象上的某个特定属性onload
   ```

   通过这种方式，事件处理器就会注册到onload事件上（当DOM已经就绪并全部构建完成，就会触发这个事件）

   ```javascript
   document.body.onclick = function(){};
   ```

   不推荐这种方法，因为一个事件只能注册一个事件处理器。

2. **通过使用内置addEventListener方法**。

   ```javascript
   function addMessage(element, message){
   	var messageElement = document.createElement("li");
   	messageElement.textContent = message;
   	element.appendChild(messageElement);
   }
   ```



## 3 函数

函数是第一类对象，拥有对象的所有能力。（**函数可以有属性**）函数是可以直接用在编程语言中的值，就像字符串和数字一样。

### 函数定义

#### 函数定义的四种方式

##### 函数声明和函数表达式（最常用）

![img](https://dpzbhybb2pdcj.cloudfront.net/maras/Figures/03fig04_alt.jpg)

```javascript
var samurai = function(){};
samurai();
```

函数内仍可以定义函数。

```javascript
function fun1() {}
var fun2 = fun1;
```

fun2只是fun1的一个引用，不会重复创建函数的实例（因为函数是第一类对象）。

**立即函数**

![img](https://dpzbhybb2pdcj.cloudfront.net/maras/Figures/03fig05_alt.jpg)

##### 箭头函数（lambda函数，ES6新增）

![img](https://dpzbhybb2pdcj.cloudfront.net/maras/Figures/03fig06_alt.jpg)

如果箭头右边是代码块，返回值与普通函数一样（如果没有return语句返回undefined，否则就是return表达式的值）。

##### 函数构造函数（动态生成，不常用）

`new function('a', 'b', 'return a+b')`

两个形参a和b，返回a+b的值（注意不同于**构造函数**）

##### 生成器函数（ES6新增）

`function *myGen() { yield 1; }`



#### 函数表达式

总是其他表达式的一部分的函数（作为赋值表达式的右值，或者作为其他函数的参数）叫做函数表达式。

如果一个函数表达式被赋值给了一个变量，可以用该变量来调用函数。

```javascript
var doNothing = function(){};
doNothing();
```

或者，如果它是另外一个函数的参数，可以在函数中通过参数名调用。

```javascript
function doNothing(action) {
  action();
}
```



### 函数参数

- 实参（argument）：调用函数时传递给函数的值
- 形参（parameter）：定义函数时列举的变量

实参数量大于形参时，多余的实参不会做赋值操作（但仍可以通过arguments参数获取它们）；相反地，如果形参数量大于实参，多余的形参会被设定为undefined。

#### 剩余参数

剩余参数是一个**数组**，只有函数的最后一个参数才能是剩余参数。

一个例子：返回第一个参数与余下参数中最大的数相乘的结果。

```javascript
function multiMax(first, ...remainingNumbers){
    var sorted = remainingNumbers.sort(function(a, b){
       return b – a;
    });
    return first * sorted[0];
  }
```

#### 默认参数

创建默认参数的方式是为形参赋值。如果指定了实参的值，默认参数会被覆盖。

对后面的默认参数赋值时可以引用前面的默认参数，如：

```javascript
function performAction(ninja, action = "skulking", message = ninja + " " + action ) {
  return message;
}
```



#### 隐式函数参数（arguments和this）

- arguments：实参集合（注意，它是一个类数组的**对象**）

  有length属性，`arguments.length`返回实参个数，但这不表示arguments是一个数组（使用`sort`方法会报错）。

  剩余参数可以作为arguments的替代（剩余参数是真正的array实例）。

  arguments对象作为函数参数的别名，更改arguments对象的值会同时改变参数值（如为`arguments[0]`赋一个新值会同时改变第一个参数的值），反之，更改参数值也会影响arguments对象。（**别名在严格模式无法使用**，使用`"use strict";`语句开启严格模式）

- this：函数上下文

  函数调用相关联的对象



### 函数调用

函数的调用方式对代码的执行的影响主要体现在this参数以及函数上下文如何建立上。



#### 函数调用的四种方式

##### 1.作为一个函数（function）——直接调用

通过()运算符调用一个函数，并且被执行的函数表达式不是作为一个对象的属性存在的。

当以这种方式调用时，函数上下文（this关键字的值）有两种可能性：

在严格模式下，它是undefined；

在非严格模式下，它是全局上下文（window对象）。



##### 2.作为一个方法（method）——关联在一个对象上，实现面向对象编程

函数被赋值给一个对象的属性，并且通过对象属性引用的方式调用。这种情况下函数被称为**方法**。

这时，该对象会成为函数的上下文，可以通过this对象访问。这也是JavaScript实现面向对象编程的主要方式之一。（另一种方式是构造函数）



##### 3.作为一个构造函数（constructor）——关键字new，实例化一个新的对象

构造函数的目的：初始化新创建的对象。

构造函数通常以不同于普通函数的方式编码和使用，并且只有作为构造函数调用时才有意义。为了区别构造函数和普通函数，我们在命名上作出约定：

- 函数和方法的命名通常以小写字母开头，动词
- 构造函数通常以大写字母开头，名词

使用关键字new调用函数时会触发以下几步动作：

1. 创建一个新的空对象
2. 该对象作为this参数传递给构造函数
3. 新构造的对象作为new运算符的返回值

![img](https://dpzbhybb2pdcj.cloudfront.net/maras/Figures/04fig01_alt.jpg)

但是有一种特例，当构造函数返回一个对象的时候，该对象将作为new运算符的返回值，并且取代this对象。如果返回的是非对象类型，则会忽略返回值。

```javascript
var puppet = {
  rules: false
};
            
function Emperor() {
  this.rules = true;
  return puppet;
}

var emperor = new Emperor(); //emperor是puppet的一个引用
console.log(emperor.rules); //false
```



##### 4.通过函数的apply/call方法

显式指定任何对象作为函数上下文。

- apply方法有两个参数，作为函数上下文的**对象**和实参**数组**。
- call方法第一个参数同apply方法，实参作为**列表**传递而非数组。

用一个例子说明：

```javascript
function juggle() {
  var result = 0;
  for (var n = 0; n < arguments.length; n++) {
    result += arguments[n];
  }
  this.result = result;
}

var ninja1 = {};
var ninja2 = {}; //建立空对象

juggle.apply(ninja1,[1,2,3,4]); //用apply方法向ninja1传递一个参数数组
juggle.call(ninja2, 5,6,7,8); //用call方法向ninja2传递一个参数数组

assert(ninja1.result === 10, "juggled via apply");
assert(ninja2.result === 26, "juggled via call");
```



##### 使用forEach方法对数组执行回调

所有数组对象均可使用forEach函数，对每个数组元素执行回调。

forEach方法有两个参数，第一个参数是需要遍历的集合，第二个参数是回调函数。



#### 函数上下文

在回调函数（如事件处理器）中，有时会出现函数上下文与预期不符的问题，一般地有三种解决方法：

##### 1.apply和call方法（上文已经提到）

##### 2.箭头函数

没有单独的this值，this在肩头函数创建时确定。

##### 3.bind方法

#### 

通过一个例子说明函数上下文

```html
<button id="test">Click Me!</button>
<script>
  function Button(){ //事件处理器的构造函数
    this.clicked = false;
    this.click = function(){
      this.clicked = true;
      assert(button.clicked, "The button has been clicked");
    };
  }
  var button = new Button(); //跟踪按钮是否被单击的实例
  var elem = document.getElementById("test");
  elem.addEventListener("click", button.click); //为按钮添加单击处理器
</script>
```

执行该程序段，发现单击按钮后，click函数的上下文对象指向了\<button\>元素，而并非button对象。这是因为浏览器的时间处理系统把调用的上下文定义为事件触发的目标元素。





### 闭包和作用域

let变量提升的一个例子  https://www.jianshu.com/p/492c660c9374

（待补充，书本ch5）





### 生成器和promise



#### 生成器（generator）





##### 生成器的工作原理



调用生成器函数不一定会执行生成器函数体。





## 9 处理集合

`map`和`set`都是ES6新增

### 数组

#### 创建

数组的创建有两种方法：

- 使用内置的`Array`构造函数
- 使用数组字面量`[]`

**推荐使用数组字面量的方法**，因为字符数量少，并且由于JavaScript的高度动态特性，无法阻止修改内置的`Array`构造函数，也就意味着`new Array()`创建的不一定是数组。

每个数组都有`length`属性，表示数组的长度。可以通过索引访问数组内元素。



```javascript
const ninjas = ["Kuma", "Hattori", "Yagyu"]
```

如果试图访问数组长度范围之外的索引，会返回`undefined`，而并不会抛出异常。

```javascript
console.log(ninjas[4]) // undefined
```

如果在数组边界之外写入元素，数组会自适应地扩大。

```javascript
ninjas[4] = "Ishi" // ninjas = ["Kuma", "Hattori", "Yagyu", undefined, "Ishi"]
console.log(ninjas.length) // 5
```

可以手动修改`length`属性的值，扩大`length`会扩展数组，新的元素均为`undefined`；缩小`length`会裁剪掉多余的元素。



#### 操作

##### 1. 在数组两端增、删元素

- `push`：在数组末尾添加元素
- `unshift`：在数组开头添加元素
- `pop`：从数组末尾删除元素
- `shift`：从数组开头删除元素

```JavaScript
ninjas = [] // 创建空数组
ninjas.push("Kuma") // ninjas = ["Kuma"]
ninjas.push("Hattori") // ninjas = ["Hattori"]
ninjas.unshift("Yagyu") // ninjas = ["Yagyu". "Hattori", "kuma"]
var last = ninjas.pop() // ninjas = ["Yagyu", "Hattori"], last = "Kuma"
var first = ninjas.shift() // ninjas = ["Hattori"], first = "Yagyu"
```

`shift`和`unshift`修改第一个元素，之后每一个元素的索引都需要调整，因此它们比`push`和`pop`方法要慢很多，非特殊情况下一般不适用。



##### 2. 在数组任意位置增、删元素

- `delete`方法
- `slice`方法

```javascript
const ninjas = ["Yagyu", "Kuma", "Hattori", "Fuma"]
delete(ninjas[1]) // ninjas = ["Yagyu", undefined, "Hattori", "Fuma"]
```

`delete`方法只是在删去的位置创建了一个空元素。

```javascript
const ninjas = ["Yagyu", "Kuma", "Hattori", "Fuma"]
var removed_items = ninjas.splice(1, 1)
console.log(removed_items) // ["Yagyu", "Hattori", "Fuma"]
console.log(ninjas) // ["Kuma"]
```

`splice`方法用于删除元素时有两个参数：起始索引和需要移除的元素个数（如果不给定会删到数组末尾的元素），后续元素会自动移动。同时，`splice`方法返回被移除的元素数组。

```javascript
const ninjas = ["Yagyu", "Kuma", "Hattori", "Fuma"]
var removed_items = ninjas.splice(1, 2, "Mochizuki", "Yoshi", "Momochi")
console.log(ninjas) // ["Yagyu", "Mochizuki", "Yoshi", "Momochi", "Fuma"]
console.log(removed_items) // ["Kuma", "Hattori"]
```

`splice`方法可以用于增加元素，本例中，先将`ninjas`数组从位置1开始删除2个元素，再插入三个元素，删除的两个元素被返回给`removed_items`变量。



##### 3. 数组遍历

- `for`循环，这里不再赘述
- `forEach`方法

使用`for`循环需要跟踪每一步的循环，以确保遍历了数组中的每一个元素。为了简化，我们可以使用JavaScript数组内置的`forEach`方法，它的参数是一个回调函数，在遍历每一个数组元素时立即执行。

```javascript
const ninjas = ["Yagyu", "Kuma", "Hattori"]
ninjas.forEach(ninja => { // ninja指代表示每次遍历到的元素
  console.log(ninja)
))
```



##### 4. 数组映射

- `map`方法

```javascript
const ninjas = [
  {name: "Yagyu", weapon: "shuriken"},
  {name: "Yoshi", weapon: "katana"},
  {name: "Kuma", weapon: "wakizashi"}
]
const weapons = ninjas.map(ninja => ninja.weapon)
console.log(weapons) // ["shuriken", "katana", "wakizashi"]
```

`map`也是数组内置方法，它创建了一个全新的数组，然后遍历输入的数组，对输入数组的每个元素，基于回调函数的执行结果创建一个相应的元素存入新的数组并返回。



##### 5. 测试数组元素

- `every`和`some`方法：用于测试数组的全部元素或部分元素是否满足某些条件。

`every`方法接受回调函数作为参数，会对每个元素执行该回调函数。如果所有数组元素的回调结果都返回`true`时，返回值为`true`。

`some`方法参数和调用方法同`every`方法，只要至少有一项元素的回调结果是`true`，返回值为`true`。

```javascript
const ninjas = [
  {name: "Yagyu", weapon: "shuriken"},
  {name: "Yoshi" },
  {name: "Kuma", weapon: "wakizashi"}
];

const allNinjasAreNamed = ninjas.every(ninja => "name" in ninja); // 检查ninjas中的元素是否有name属性
const allNinjasAreArmed = ninjas.every(ninja => "weapon" in ninja); // 检查ninjas中的元素是否有weapon属性
console.log(allNinjasAreNamed) // true
console.log(allNinjasAreArmed) // false

const someNinjasAreArmed = ninjas.some(ninja => "weapon" in ninja);
console.log(someNinjasAreArmed) // true
```

在这个例子中的第8行，在检查`ninjas`的每个元素是否有`weapon`属性时，检查到第二个元素`{name:"Yoshi"}`时返回`false`，这时不再向后检查，直接返回`false`。

同样地，在第12行，当检查到第一个元素时返回了`true`，之后不再继续向后检查，直接返回`true`。



##### 6. 数组查找

- `find`方法：接收回调函数，返回满足条件的第一个元素

- `filter`方法：接收回调函数，返回满足条件的多个元素
- `indexOf`和`lastIndexOf`方法：接收元素，返回索引值
- `findIndex`方法：接收回调函数，返回索引值

```JavaScript
const ninjas = [
  {name: "Yagyu", weapon: "shuriken"},
  {name: "Yoshi" },
  {name: "Kuma", weapon: "wakizashi"}
]

const ninjaWithWakizashi = ninjas.find(ninja => { // 使用find查找单个元素
  return ninja.weapon === "wakizashi"
})
console.log(ninjaWithWakizashi) // {name:"Kuma", weapon:"wakizashi"}

const ninjaWithKatana = ninjas.find(ninja => {
  return ninja.weapon === "katana";
});
cosole.log(ninjaWithKatana) // undefined

const armedNinjas = ninjas.filter(ninja => "weapon" in ninja); // 使用filter查找多个元素
console.log(armedNinjas) // [{name:"Yagyu", weapon:"shuriken"}, {name:"Kuma", weapon:"wakizashi"}]
```

`find`和`filter` 的参数都是回调函数，`find`返回第一个回调函数返回`true`的元素，`filter`返回包含全部回调函数返回`true`的元素的数组。如果不存在符合条件的元素，则返回`undefined`。

```JavaScript
const ninjas = ["Yagyu", "Yoshi", "Kuma", "Yoshi"]
console.log(ninjas.indexOf("Yoshi")) // 1
console.log(ninjas.lastIndexOf("Yoshi")) // 3

const yoshiIndex = ninjas.findIndex(ninja => ninja === "Yoshi");
console.log(yoshiIndex); // 1
```

`indexOf`查找特定元素的索引，参数为目标元素，返回元素索引值。

在数组中有多个特定元素时，使用`lastIndexOf`可以返回最后一次出现该元素的索引值。

`findIndex`用于查找元素，接收的参数是回调函数，并返回第一个回调函数返回`true`的元素，本质上与`find`类似。



##### 7. 数组排序

- `sort`方法

`sort`方法需要提供回调函数，来告诉排序算法**相邻**的两个数组元素的关系。

`sort`使用的方法是：`array.sort((a, b) => a - b)`，它与数组元素的关系是：

![img](https://dpzbhybb2pdcj.cloudfront.net/maras/Figures/09fig10_alt.jpg)



##### 8. 合计数组元素

- `reduce`方法

阅读资料： https://www.jianshu.com/p/e375ba1cfc47

`reduce`方法接收两个参数，第一个是一个回调函数，第二个是初始值。

回调函数可以接收4个参数：初始值（或者上一次回调函数的返回值），当前元素值，当前索引，调用 reduce 的数组。后两个参数是可选参数。

一个简单地累加`numbers`数组元素值的实例：

```javascript
const numbers = [1, 2, 3, 4]
const sum = numbers.reduce((aggregated, number) => aggregated + number, 0) // 0 is the initial, and the callback is an arrow function
console.log(sum) // 10
```



书本9.1.5节没有理解，笔记待补  复用内置数组函数 `Array.prototype.push`



### Map

相关资料：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Map

#### 创建 & 方法

使用新的内置构造函数`Map`。

```javascript
const ninjaIslandMap = new Map(); // 创建map

const ninja1 = { name: "Yoshi"};
const ninja2 = { name: "Hattori"};
const ninja3 = { name: "Kuma"}; // 定义三个ninja对象

ninjaIslandMap.set(ninja1, { homeIsland: "Honshu"});
ninjaIslandMap.set(ninja2, { homeIsland: "Hokkaido"}); // 使用set方法建立映射关系

// 用get方法获取value
console.log(ninjaIslandMap.get(ninja1).homeIsland === "Honshu") // true

// 用has方法确定key是否存在
console.log(ninjaIslandMap.has(ninja1)) // true
console.log(ninjaIslandMap.has(ninja2)) // true
console.log(ninjaIslandMap.has(ninja3)) // false

ninjaIslandMap.delete(ninja1); // 删除key
// 
console.log(!ninjaIslandMap.has(ninja1) && ninjaIslandMap.size() === 1) // true

ninjaIslandMap.clear(); // 清空map
```

本例中的映射关系如图：

![img](https://dpzbhybb2pdcj.cloudfront.net/maras/Figures/09fig14.jpg)



#### 遍历

- `for...of`循环

```javascript
var myMap = new Map()
myMap.set(0, "zero")
myMap.set(1, "one")

for (var [key, value] of myMap) { // 遍历键值对
  	console.log(key, value)
}

for (var key of myMap.keys()) {
    console.log(key)
}

for (var value of myMap.values()) {
    console.log(value)
}
```

- `forEach`方法

```JavaScript
myMap.forEach(function(value, key) {
  console.log(key + " = " + value)
})
```





### Set



#### 创建 & 方法 & 遍历

使用构造函数`Set`，接收数组作为参数

```JavaScript
const ninjas = new Set(["Kuma", "Hattori", "Yagyu", "Hattori"]) // 新建set并初始化
```

`Set`成员的值都是唯一的，可以使用`add`方法多次添加相同值，但是只成功添加一次

```javascript
ninjas.add("Yoshi")
```

`has`方法用于验证`Set`中是否存在元素

```javascript
ninjas.has("Yoshi")
```

与`Map`和数组类似，`Set`也是集合，因此可以使用`for-of`循环进行遍历。成员的遍历顺序与插入的顺序一致。



#### 并集 & 交集 & 差集

并集（Union）

```JavaScript
const ninjas = ["Kuma", "Hattori", "Yagyu"]
const samurai = ["Hattori", "Oda", "Tomoe"]

const warriors = new Set([...ninjas, ...samurai]) // warriors是ninjas和samurai的并集
```

交集（Intersection）

```javascript
const ninjas = new Set(["Kuma", "Hattori", "Yagyu"])        
const samurai = new Set(["Hattori", "Oda", "Tomoe"])      

const ninjaSamurais = new Set(
  [...ninjas].filter(ninja => samurai.has(ninja))
) // ninjaSamurais是ninjas和samurai的交集
```

差集（Difference）

```JavaScript
const ninjas = new Set(["Kuma", "Hattori", "Yagyu"])         
const samurai = new Set(["Hattori", "Oda", "Tomoe"])      

const pureNinjas = new Set(
  [...ninjas].filter(ninja => !samurai.has(ninja))
) // pureNinjas是ninjas减去samurai的差集
```

是`filter`方法只能操作数组，我们使用`...`将`Set`转换成数组。

