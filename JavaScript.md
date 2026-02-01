### 1. 原型与原型链

#### 1.1 原型与原型链的理解

**原型链**是实现==对象继承==的核心机制，要想完整理解对象集成，我们首先需要了解以下概念：

- **构造函数 constructor**：可通过 new 关键字调用的函数，如 Person()
  - **构造函数中的 `this` 就是实例 **
  - 构造函数的方法一般挂载到 `prototype` 上
- **实例对象**：构造函数通过 `new` 构造的对象 `const person = new Person()`，person 就是 Person 的实例。 
  - 实例对象包含一个 construct 属性指向他的构造函数 `person.constructor` 指向 Person()

- **原型对象 prototype**：也称**原型**，是构造函数的一个属性，包含实例对象所**共享**的属性和方法
  - ==原型对象永远指向另一个对象或者是 null==，我们也可以理解为**原型对象也是另外一个对象的实例**，因此我们可以通过 `__proto__`来获取"原型对象的原型对象"
  - 由于原型对象是构造函数的一部分，因此我们可以通过 `Person.prototype` 获取 Person 的原型对象，同理我们可以通过 `Person.prototype.constructor`来获取原型对象的构造函数
  - 我们可以通过 `person.__proto__`来获取实例原型，实例会从原型上继承属性和方法。也可以通过 `__proto__`来指定实例的原型。
  - **原型对象的 `this` 也指向当前实例本身**
- **原型链**：由原型对象层层继承的链接结构，通过 `__proto__` 属性链接
  - ==当实例对象上不存在某个属性或方法时，会**沿着原型链向上查找**，直到找到或者到达原型链顶端为止==
  - **原型链的顶端为 `Object.porototype`，原型链的终点为 null ，即 `Object.porototype --> null`**

![image-20250807095258778](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20250807095258778.png)

![image-20250807095817128](https://gitee.com/phenylaminopropionic-acid/photo/raw/master/image-20250807095817128.png)

#### 1.2 获取和写入原型

**继承的对象运行继承的方法时，它们将仅修改自己的状态，而不会修改 原型对象 的状态**

```javascript
// case 1
let user = {
  name: "John",
  surname: "Smith",

  set fullName(value) {
    [this.name, this.surname] = value.split(" ");
  },

  get fullName() {
    return `${this.name} ${this.surname}`;
  }
};

let admin = {
  __proto__: user,
  isAdmin: true
};

// 由于 admin 的原型 user 存在 name 和 surname 属性，因此会调用 get 直接获取
alert(admin.fullName); 

// 此时调用了 set 方法修改了实例对象admin的 name 和 surname 属性，此时的 this 为 admin
admin.fullName = "Alice Cooper"; 

alert(admin.fullName); // Alice Cooper，admin 的内容被修改了
alert(user.fullName);  // John Smith，user 的内容被保护了

// case 2
let animal = {
  sleep() {
    this.isSleeping = true;
  }
};

let rabbit = {
  name: "White Rabbit",
  __proto__: animal
};

// 修改 rabbit.isSleeping
rabbit.sleep();

alert(rabbit.isSleeping); // true
alert(animal.isSleeping); // undefined（原型中没有此属性）
```



### 2. this 指向与绑定

- ==`this` 总是返回一个对象==

* this 是执行上下文中的一个属性（在执行上下文被创建时确定的），它的值在**==函数被调用==**时动态确定
* **在函数执行过程中，this一旦被确定，就不可更改了**。

#### 2.1 以==函数的形式==调用时，this 默认指向全局对象

- 浏览器中 `this` 指向 `window`，`Nodejs` 中指向 `global`

- **严格模式下，`this` 会被绑定到 `undefined` 下**

```js
function showThis() {
  console.log(this);
}

// 严格模式（'use strict'）下输出: undefined
showThis(); // 浏览器中输出: Window {...}（非严格模式）
```

```javascript
var foo = {
    bar:10;
    fn(){
        console.log(this)
        console.log(this.bar)
    }
}

var fn2 = foo.fn
fn2() // 输出: window undefined

// 解析： fn2() 等价于直接调用 fn() 此时 fn 的 this 指向全局对象 window，并且 window 下是
//       不存在 bar 属性的，因此输出 undefined
```

#### 2.2 当函数作为==对象的方法==调用时，this 指向调用该对象

```js
const user = {
  name: "小明",
  greet: function() {
    console.log(this.name); // this → user 对象
  }
};

user.greet(); // 输出: "小明"
```

⚠️ ==**隐式丢失陷阱**：**方法被赋值后调用会丢失原对象绑定！**==

```js
const greet = user.greet;
// 此时可以视为函数被独立调用，故 this 指向了全局对象 window 或 undefined(严格模式下)
greet(); // 输出: undefined（严格模式）或全局对象上的 name（非严格模式）
```

#### 2.3 使用 *new* 方法调用构造函数，this 指向该对象

> 构造函数与原型对象的 this 都被实例对象所拥有，每个实例对象的 this 都是独立的

```js
function Person(name, age) {
    this.name = name;
    this.age = age; 
}

Person.prototype.getName = function() {
     return this.name;
  }

// nick 为 Person 的实例对象，因此 Person 中的 this 指向 nick
var nick = new Person(’Nick‘, 20);
// nick 使 getName 函数的调用者，因此 getName 函数中的 this 也指向 nick
nick.getName();
```

通过new操作符调用构造函数，会经历以下4个阶段：

* 创建一个新的对象；

* **==将构造函数的this指向这个新创建的实例对象==**；

* 指向构造函数的代码，为这个对象添加属性，方法等；

* 返回新对象。

#### 2.4 箭头函数的 this

==箭头函数并**没有属于⾃⼰的this**==，它所谓的this是**捕获其==外层作用域==的 this 值**，作为自己的this 值。

> 由于箭头函数没有属于⾃⼰的this，因此箭头函数不能用作构造器，所以是**不会被new调⽤**的，这个所谓的this也不会被改变。

```js
const obj = { 
  getArrow() { 
    return () => { 
      console.log(this === obj); 
    }; 
  } 
}

var x = 20
const obj = { 
   x:10,
   test:() => { 
      console.log(this); 
      console.log(this.x); 
    }; 
  } 
}

obj.test() // 输出 window 20
// 解析：这里的 this 其实指向 obj 对象的外层，即浏览器 window
```

#### 2.5 绑定事件的 this

- DOM 处理绑定事件时，**==事件处理函数的 `this` 指向绑定事件的元素==**
  - ==**事件处理函数的 `target` 指向触发事件的元素**==

#### 2.6 ==使用call、apply显式指定 this==

* 它们的**作用一模一样**，区别仅在于传入参数的形式的不同。 

* `apply` 接受**两个参数**，**第一个参数指定了函数体内 this 对象的指向**， 第二个参数为一个带下标的**集合**，这个**集合可以为数组，也可以为类数组（`arguments` 数组**），`apply` 方法把这个集合中的元素作为参数传递给被调用的函数。 

  * ```javascript
    const arr = [1,2,3,4,5]
    // 通过 apply 获取数组最大值
    console.log(Math.max.apply(null, arr))
    ```

* `call` 接受**多个参数**，跟 apply 相同的是，**第一个参数也是代表函数体内的 this 指向**，从第二个参数开始往后，每个参数被依次传入函数

* 注意：

  * **==如果 `call/apply` 传递的对象为 `''/null/undefined` 则此时 `this` 仍指向全局==**
  * **如果传递的值为 `number/true` 等类型，则 `this` 会指向对应的==包装对象==**


```js
function fn(num1, num2) {
    console.log(this.a + num1 + num2);
}
var obj = {
    a: 20
}

fn.call(obj, 100, 10); // 130
fn.apply(obj, [20, 10]); // 50
```

#### ==2.7 总结：==

```
函数调用方式 → 决定 this 指向
├─ new 调用 → 实例对象
├─ call/apply/bind → 指定的对象
├─ 对象方法调用 → 调用者对象
├─ 箭头函数 → 外层作用域的 this
└─ 普通函数调用 → 全局对象或 undefined（严格模式）
```



### 3. 执行上下文

#### 3.1 基本概念

执行上下文：可以看作是代码执行的**"运行环境"**，`javascript`中的所有代码都是在执行上下文中执行的。

执行栈：控制代码的**执行顺序**的**调用栈（后进先出）**。每当有函数被调用，都会在**栈顶**为该函数创建一个上下文，处于**栈顶的上下文会被优先执行**，执行完毕后弹出将控制权交还给下一个执行上下文。

在`javascript`中存在三种上下文，分别是：

- **全局上下文：进入代码时创建（window 对象），永远处于栈底**
- **函数上下文**：在 `function` 被函数调用时创建
- ~~Eval 函数上下文~~

#### 3.2 执行上下文生命周期

##### 3.2.1 创建阶段

在创建阶段，一共会处理三件事情，分别是：

- **确定this的值**（见上文）
- 变量和函数声明（**将变量和函数加入到花名册**）
  - 变量声明又分为**变量环境**和**词法环境**
    - `var` 声明的变量和 `function` 声明的函数都存在变量提升，会被提升到**==当前作用域==的顶端**
  - **顶级函数声明会直接在全局声明该函数，并立即初始化创建该函数对象**

| **特性**           | **变量环境（Variable Environment）**           | **词法环境（Lexical Environment）**              |
| ------------------ | :--------------------------------------------- | :----------------------------------------------- |
| **存储内容**       | `var` 声明的变量、**顶级函数**声明             | `let/const` 声明的变量、块级作用域               |
| **作用域规则**     | 函数作用域或全局作用域                         | 块级作用域（如 `if/for`）                        |
| **变量提升**       | 存在并初始化为 `undefined`，挂载到 `window` 中 | 存在但不可访问`uninitialized`，挂载到 `scope` 中 |
| **块级作用域处理** | 无视块，穿透到函数或全局作用域                 | 严格遵循块级作用域                               |

```javascript
var a = 100 
// 1. var a: undefined --> 创建阶段
// 2. a = 100 --> 执行阶段
```

- **创建作用域链**（见下文）

##### 3.2.2 执行阶段

**按照代码顺序**依次进行**变量赋值**、**代码执行**、**函数引用**等操作

##### 3.2.3 销毁阶段

![img](https://cdn.nlark.com/yuque/0/2025/png/29434615/1739085920665-4b4ecc30-6426-433d-8178-e50265ae16f0.png?x-oss-process=image%2Fformat%2Cwebp)



### 4. 作用域与作用域链

#### 4.1 什么是作用域 Scope

作用域是指**变量的==可用性代码范围==**，`javascript`中包含了三种不同的作用域，分别是：

- 全局作用域：通常是浏览器中的 `window` 对象或 Node.js 中的 `global` 对象

  - 在浏览器中，任何**在全局作用域中声明的变量都会被挂载到 `window`对象下** 

  ```javascript
  let globalVar = "Global Variable";
  console.log(window.globalVar);  // 输出：Global Variable
  ```
- 函数作用域：**函数执行时生成的作用域**，在函数内部生效
- 块级作用域：由`let/const` 声明加上最近的 `{}`所包裹的代码形成一个块级作用域。

```javascript
// { } 形成了一个块级做作用域
{
	let a = 100
    const b = 200
    console.log(a, b)
    
    // { } 属于内部的块级作用域
    {
        let c = 300
    }
}
```

==**作用域是分层的**，**内层作用域可以访问外层作用域**，反之不行==

```javascript
let globalVar = "I am global";  // 全局变量

function testScope() {
  let localVar = "I am local";  // 局部变量
  console.log(globalVar);  // 可以访问全局变量
  console.log(localVar);   // 可以访问局部变量
}

testScope();
console.log(globalVar);  // 可以访问全局变量，输出：I am global
console.log(localVar);   // 错误：外部作用域无法访问局部变量
```

#### 4.2 什么是作用域链

- 作用域链是 `javascript` 中用于**查找变量和函数**的一种机制，本质上是**变量查找的路径**。
- 每个 `javascript` 函数都会创建一个作用域链
- 如果**在自己作用域找不到该变量就去父级作用域查找**，依次向上级作用域查找，直到访问到全局对象就被终止。如果在整个作用域链中都无法找到该变量，则会抛出 `ReferenceError` 异常。

**作用域链保证了当前执行环境对符合访问权限的变量和函数的有序访问**，由于作用域链的存在，因此形成了一个新的概念：闭包

#### 4.3 闭包 Closure

##### 4.3.1 什么是闭包

- 闭包是一种特殊的**对象**，通过 **保留作用域链** 冻结变量。==**闭包 = 函数 + 其创建时所在的作用域链**==
- **通过闭包，我们可以在其他的执行上下文中，访问到函数的内部变量**

```mermaid
graph LR
  A[函数创建] --> B[绑定当前词法环境]
  B --> C[环境记录存储变量]
  C --> D[外部引用指向父级环境]
  D --> E[形成作用域链]
```

```javascript
function outerFunction() {
    let outerVariable = '我在outer函数里!';
  
    function innerFunction() {
      console.log(outerVariable);
    }
  
    return innerFunction;
  }
  
  const innerFunc = outerFunction();
  innerFunc(); // 输出: 我在outer函数里!
```

##### 4.3.2 闭包的使用场景

- 创建**私有变量**
- **==延长变量的生命周期==**，防止变量被回收 --> 可能导致**内存泄漏**
- **柯里化函数**：避免频繁调用具有相同参数函数的同时，又能够轻松的重用
- **计数器、延迟调用、回调等闭包的应用，其核心思想还是创建私有变量和延长变量的生命周期**



### 5. 立即执行函数

#### 5.1 什么是立即执行函数

立即执行函数是一种特殊的函数表达式，它**无需显示调用，会在函数声明后立即执行该函数**，执行完毕后立即销毁。

- 立即执行函数**无需命名**，可以**避免污染全局变量**
- 立即执行函数内部形成了**单独的作用域**，可以用于**封装私有变量**

```javascript
// 立即执行函数示例
(function(){
    console.log("我是匿名立即执行函数")
})()
(function(){
    console.log("我是匿名立即执行函数")
}())

// case 1
var fn1 = function fn(){
    console.log("我是匿名立即执行函数fn1")
}()
console.log(fn1) // undefined

// case 2
(function fn2(){
    let a = 100
})()
console.log(fn2) // 报错
console.log(a) // 报错, 无法在外部直接访问立即执行函数内部定义的变量

// case 3
let a;
(function fn3(){
    a = 100
})()
console.log(a) // 100
```

- 匿名执行函数可以传参 --> **参数属于函数作用域的私有变量，使用参数时会形成闭包**

```javascript
// 立即执行函数传参
(function(name){
    console.log(name) // 输出：kiri
})("kiri")

(function(name, age){
    console.log(name + age + "岁") // 输出：kiri18岁
})("kiri", 18)
```



### 6. 数组方法

#### 6.1 会影响原数组的方法

- **`push()`**：在数组末尾添加元素，**返回新数组长度**
- **`unshift()**`：在数据头部添加元素，**返回新数组长度**
- **`pop`**：删除数组末尾元素，**返回被删除元素**
- **`shift`**：删除数组头部元素，**返回被删除元素**
- **`join(params:string)`**：将数组转换为字符串并通过 `params` 拼接，**返回拼接后的字符串**
- **`reverse()`** ：颠倒数组中元素的顺序，**返回顺序颠倒后的数组**

```javascript
let array = [1,2,3,4,5]
array.reverse()  // [5, 4, 3, 2, 1]
```

- **`splice(start, delCount, ...items)`** ：**返回被删除元素组成的数组**

  - `start`：开始操作的位置索引`
  - `delCount`：要删除的元素个数
  - `item1, item2...`：要插入的新元素（可选）

  ```javascript
  // 删除
  let arr = [1, 2, 3, 4];
  let deleted = arr.splice(1, 2); // 从索引1开始，删除2个元素
  console.log(arr);    // [1, 4]
  console.log(deleted); // [2, 3]
   
  // 插入
  let arr = [1, 4];
  arr.splice(1, 0, 2, 3); // 在索引1处插入2、3，删除0个
  console.log(arr); // [1, 2, 3, 4]
   
  // 替换（改 = 删 + 增）
  let arr = [1, 2, 3, 4];
  arr.splice(1, 2, 'a', 'b'); // 从索引1开始，删除2个，插入 'a', 'b'
  console.log(arr); // [1, 'a', 'b', 4]
   
  // 删除所有之后的元素
  let arr = [1, 2, 3, 4, 5];
  arr.splice(2); // 从索引2开始，全部删除
  console.log(arr); // [1, 2]
   
  // 只删除最后一项（可替代 pop()）
  let arr = [10, 20, 30];
  arr.splice(-1, 1); // 删除最后一个
  console.log(arr); // [10, 20]
  ```

- **`fill(value, start, end)`**：从 索引 `start 到 end-1` 用值 `value` 填充数组，**返回新数组**

  - `value`: 要填充的值
  - `start`：开始填充的位置（包含）
  - `end`：结束位置（不包含）

  ```javascript
  //填满整个数组
  let arr = [1, 2, 3];
  arr.fill(0);
  console.log(arr); // [0, 0, 0]
   
  //指定开始位置
  let arr = [1, 2, 3, 4, 5];
  arr.fill(9, 2); // 从 index 2 开始填充
  console.log(arr); // [1, 2, 9, 9, 9]
   
  //指定开始+结束位置
  let arr = [1, 2, 3, 4, 5];
  arr.fill(7, 1, 4); // 1~3填充7
  console.log(arr); // [1, 7, 7, 7, 5]
   
  //创建新数组并填充（常用技巧）
  let arr = new Array(5).fill(0);
  console.log(arr); // [0, 0, 0, 0, 0]
  ```

#### 6.2 不会影响原数组的方法

- **`slice(a, b)`** ：截取数组标为 `a ~ b-1` 区间的元素，**返回的是截取区间的新数组**

> 可以只传一个参数，此时会从该参数位置开始截取到数组最后一项

```javascript
let array = [1,2,3,4,5]
array.slice(1, 3) // [2, 3]
array.slice(1) // [2, 3, 4, 5]
```

- **`concat(arr1, arr2...)`**：用于将多个数组合并为同一个数组，**返回合并后的新数组**

```javascript
let a = [1, 2];
let b = [3, 4];
let result = a.concat(b);
console.log(result); // [1, 2, 3, 4]
```

- **`reduce(callback, initial)`**

```javascript
array.reduce(function(accumulator, currentValue, index, array) {
  return 处理逻辑;
}, initialValue);

/**
· accumulator：累加器（上一次返回的结果）
· currentValue：当前正在处理的元素
· index：当前索引（可选）
· array：原数组（可选）
· initialValue：初始值, 如果不传则会默认使用第一个元素作为初始值，每次迭代回调返回值作为新的累加器
*/


//求和（最常用）
const arr = [1, 2, 3, 4];
const sum = arr.reduce((acc, cur) => acc + cur, 0);
console.log(sum); // 10
 
//求最大值 / 最小值
const arr = [5, 3, 9, 1];
const max = arr.reduce((acc, cur) => Math.max(acc, cur));
console.log(max); // 9
 
//对象数组求总和（如价格、数量）
const cart = [
  { name: '苹果', price: 3, qty: 2 },
  { name: '香蕉', price: 2, qty: 5 }
];
const total = cart.reduce((acc, item) => acc + item.price * item.qty, 0);
console.log(total); // 3*2 + 2*5 = 16
```

- **`includes(value)`**：检查数组中是否存在元素 `value`，**返回 `true/false`**

```javascript
let array = [1,2,3,4,5]
array.includes(6) // false
array.includes(3) // true
```

- **`indexOf(value)`**：**返回 `value` 在数组中第一次出现的下标**，查找不到返回 -1

 ```javascript
 let array = [1,2,2,3,2,4,5];
 array.indexOf(2) // 1
 array.indexOf(0) // -1
 ```

-  **`lastIndexOf(value)`**：**返回 `value` 在数组中最后一次出现的下标**，查找不到返回 -1

```javascript
let array = [1,2,2,3,2,4,5];
array.lastIndexOf(2) // 4
array.lastIndexOf(0) // -1
```

- **`some(callback)`**：**只要有一个元素符合条件就返回 `true`** 

```javascript
let array = [
 {id:1,name:'张三'},
 {id:2,name:'王二'},
 {id:3,name:'李四'}
]
array.some(item => item.id == 2) // true
array.some(item => item.id == 0) // false
```

- **`every(callback)`**：**所有元素都符合条件猜返回 `true`** 

```javascript
let array = [
 {id:1,name:'张三',flag:true},
 {id:2,name:'王二',flag:true},
 {id:3,name:'李四',flag:true}
]
array.every(item => item.flag == true) // true
array.every(item => item.id == 2) // false
```

-  **`find(callback`**)：**返回第一个符合条件的值**，如果找不到则返回 `undefined`。

```javascript
let array = [
 {id:1,name:'张三'},
 {id:2,name:'王二'},
 {id:3,name:'李四'}
]
array.find(item => item.id == 1) // {id=1,name='张三'}
```

- **`findIndex(callback)`**：**返回第一个符合条件的索引**，如果找不到则返回 `-1`

```javascript
let array = [
 {id:1,name:'张三'},
 {id:2,name:'王二'},
 {id:3,name:'李四'}
]
array.find(item => item.id == 1) // 0
```

- **`map(callback)`**：遍历所有元素并执行对应的操作，**返回新数组**

```javascript
let array = [1,2,3];
let newArray = array.map(item=>{
  return {id:item,name:'张三'+item}
})
console.log(newArray)
/*
[
 {id:1,mane:'张三1'},
 {id:2,mane:'张三2'},
 {id:3,mane:'张三3'}
]
*/
```

- **`filter(callback)`**：过滤符合条件的元素，**返回新数组**

```javascript
//筛选偶数
const arr = [1, 2, 3, 4, 5, 6];
const even = arr.filter(item => item % 2 === 0);
console.log(even);  // [2, 4, 6]
 
//过滤字符串
const names = ['Tom', 'Jerry', 'Tony', 'Jack'];
const result = names.filter(name => name.startsWith('T'));
console.log(result);  // ['Tom', 'Tony']
 
//过滤对象数组（常用于实际开发）
const users = [
  { id: 1, name: '张三', active: true },
  { id: 2, name: '李四', active: false },
  { id: 3, name: '王五', active: true },
];
const activeUsers = users.filter(user => user.active);
console.log(activeUsers);
/* [
    { id: 1, name: '张三', active: true },
    { id: 3, name: '王五', active: true }
]*/
```

- **`forEach(callback)`** ：遍历数组每一项并操作，**无返回值**

```javascript
let arr = [1, 2, 3];
arr.forEach(x => console.log(x)); // 1 2 3
```

#### 6.3 数组的遍历方式

- `for` 循环

```javascript
for (let i = 0; i < array.length; i++) {
  console.log(array[i]);
}
```

- `forEach`

```javascript
array.forEach(function(currentValue, index, array) {
  // 你的代码
}, thisArg);
```

- `map`

```javascript
let newArray = array.map(function(currentValue, index, array) {
  return 
}, thisArg);
```

- `for...of...` 循环

```javascript
for (let item of array) {
  console.log(item);
}
```

- `filter()/some()/find()` 等都会遍历数组

#### 6.4 引申：类数组

##### 6.4.1 什么是类数组

类数组对象通常指的是那些拥有 `length` 属性，并且可以通过索引来访问元素的对象。**具有数组结构**，但**不拥有数组方法**的**普通对象**。通常需要将类数组转换为真正的数组进行操作。

常见的类数组对象包括：

- **`arguments` 对象**
- `NodeList` 和 `HTMLCollection` 对象 

```javascript
function logArguments() {
  const args = arguments; // 类数组对象
  console.log(args.length); // 输出参数的数量
  console.log(args[0]); // 输出第一个参数
}

logArguments('apple', 'banana', 'cherry');
// Output:
// 3
// apple
```

##### 6.4.2 类数组使用场景

- 函数参数处理：`arguments` 对象常用于函数内部，尤其是在函数参数数量不确定时。

> 引申：在 ES6 后可以使用剩余参数 `...参数名` 来代替 arguments 对象来获取参数。

```javascript
const sum = () => {
  const args = arguments; // 类数组对象
  return Array.from(args).reduce((acc, val) => acc + val, 0);
}

// 使用剩余参数代替 arguments 对象
const sum = (...args) => {
  return Array.from(args).reduce((acc, val) => acc + val, 0);
}

console.log(sum(1, 2, 3, 4)); // Output: 10
```

- **DOM 操作**：许多 DOM 方法返回类数组对象，例如 `querySelectorAll()`

```javascript
const listItems = document.querySelectorAll('li'); // 返回类数组对象
const itemsArray = Array.from(listItems);
itemsArray.forEach(item => console.log(item.textContent));
```

##### 6.4.3 类数组转换为数组对象

- **`Array.from()`**

```javascript
const nodeList = document.querySelectorAll('div');
// 转换为真正的数组
const arrayFromNodeList = Array.from(nodeList);
```

- 使用**展开运算符 ...**

```javascript
const nodeList = document.querySelectorAll('p');
// 转换为真正的数组
const arrayFromNodeList = [...nodeList];
```

- 使用 **`Array.slice()`**



### 7. 事件循环 Event Loop

**事件循环**是  `javascript` 中用来**处理异步任务**的机制，确保**非阻塞**的任务执行

- 单线程： `javascript` 的执行是**==单线程==**的，所有代码都在一个线程中运行
- **同步任务与异步任务：同步任务会立即执行，而异步任务（如 `setTimeout`、网络请求）不会阻塞主线程，它们的==回调==会推迟到稍后执行**
- **调用栈：用于管理同步任务**。每当 `javascript` 执行一个函数调用，它会被压入调用栈，执行完毕后弹出。
- **任务队列：异步任务的回调函数**会被放入任务队列，等待调用栈清空后执行
- ==**事件循环**：事件循环不断检查调用栈是否为空，如果为空，它会从任务队列中取出任务并执行==

![image-20250708160727853](https://gitee.com/phenylaminopropionic-acid/photo/raw/master/image-20250708160727853.png)

#### 7.1 任务队列

任务队列划分为**宏任务队列`Macro Task Queue`和微任务队列 `Micro Task Queue`**：

==**同步优先，微任务插队，宏任务排队**，**每次执行宏任务前，必须清空微任务队列**==

|     特性     |                     同步任务                     |                            微任务                            |                    宏任务                    |
| :----------: | :----------------------------------------------: | :----------------------------------------------------------: | :------------------------------------------: |
| **执行时机** |                  主线程立即执行                  |                       在渲染前立即执行                       |                 在渲染后执行                 |
| **队列处理** |              无队列，直接按顺序执行              |                      **一次性清空队列**                      |               每次只取一个任务               |
|  **优先级**  |                       最高                       |                     次高（仅次同步代码）                     |                     最低                     |
| **常见来源** | 普通代码（如 `console.log`）⭐**`new Promise(`)** | **`Promise.then/catch/finally`** **`$nextTick`**、DOM更新、`MutationObserver` | `setTimeout/setInterval` 、I/O回调、交互事件 |

#### 7.2 事件循环流程（类似于服务员的一天）：

1. 先处理所有到店顾客（同步代码）
   `console.log(”欢迎光临“); // 立刻执行`
2. 遇到特殊需求就记下来

* 外卖订单（微任务）→ 记到外卖窗口的小本本上→ 加入微任务队列

  `Promise.resolve().then(() => { console.log(”您的外卖已接单“) })`

* 预约顾客（宏任务）→ 贴到堂食排队区的公告栏 → 加入宏任务队列

  `setTimeout(() => { console.log(”预约顾客到店“) }, 0)`

3. 完成当前所有到店顾客后，服务员会：

* 先跑一趟外卖窗口，处理所有加急订单（清空微任务队列）
* 再去堂食排队区叫一个顾客过来点餐（执行一个宏任务）
* 重复这个流程直到打烊

```javascript
console.log(”【服务员】开始接待顾客“);
setTimeout(() => { console.log(”【后厨】红烧肉做好了“) }, 0);
Promise.resolve().then(() => { console.log(”【外卖】奶茶已打包“) });
console.log(”【服务员】当前顾客点完菜“);

// 输出顺序：
// 1. 【服务员】开始接待顾客
// 2. 【服务员】当前顾客点完菜
// 3. 【外卖】奶茶已打包
// 4. 【后厨】红烧肉做好了
```



### 8. Set 和 Map

#### 8.1 Set 集合

Set 是一种特殊的数据集合，具有以下特征：

- **唯一性**：`Set` 中的每个值只能出现一次，**重复的值会被自动忽略**
- **无序性**：`Set` 中的值**没有顺序**
- **可迭代**：`Set` 支持 `for...of` 循环和 `forEach` 方法
- **值的类型**：`Set` 可以存储任意类型的值

##### 8.1.1 创建 Set 集合

```javascript
const set = new Set(); // 创建一个空集合

const set1 = new Set([1, 2, 3])
console.log(set1)  // Set(3) {1, 2, 3}
```

##### 8.1.2 Set 方法

- `set.add(value)`：向 Set 集合中添加元素 value，**重复的值不会被添加**

```javascript
set.add(1); 
set.add(2); 
set.add(2); // 重复值会被忽略 
console.log(set); // 输出 Set(2) { 1, 2 }
```

- `set.has(value)`：检查 Set 集合中是否存在某个值 value，如果**存在则返回 true**

```javascript
console.log(set.has(1)); // 输出 true
console.log(set.has(3)); // 输出 false
```

- `set.delete(value)`：删除 Set 集合中的某个值 value，**删除成功则返回 true**

```javascript
set.delete(1) // true
set.delete(3) // false
console.log(set); // 输出 Set(1) {1}
```

- `set.clear()`：清空 Set 集合

```javascript
set.clear();
console.log(set); // 输出 Set {}
```

- `set.size()`：返回 Set 集合的大小

```javascript
console.log(set1.size()) // 输出 3
```

##### 8.1.3 Set 遍历

- `for ... of`循环

```javascript
const set = new Set([1, 2, 3]);
 
for (const value of set) {
  console.log(value); // 依次输出 1, 2, 3
}
```

- **`set.forEach()`遍历**

```javascript
set.forEach((value) => {
  console.log(value); // 依次输出 1, 2, 3
});
```

- `set.values()` 和 `set.keys()` : 返回包含 Set 集合所有**值**的迭代器 --> Set 没有键

```javascript
// set.values()
const valuesIterator = set.values();
for (const value of valuesIterator) {
  console.log(value); // 依次输出 1, 2, 3
}

// set.keys()
const keysIterator = set.keys();
for (const key of keysIterator) {
  console.log(key); // 依次输出 1, 2, 3
}
```

- `set.entries()`：返回一个包含 `[value, value]` 对的迭代器 ( 两者都是值 )

```javascript
const entriesIterator = set.entries();
for (const [key, value] of entriesIterator) {
  console.log(key, value); // 依次输出 1 1, 2 2, 3 3
}
```

##### 8.1.4 Set 应用场景 --> 利用 Set 集合不包含重复元素的特性

- **数组去重** 

```javascript
const array = [1, 2, 2, 3, 4, 4];
const uniqueArray = [...new Set(array)];
console.log(uniqueArray); // 输出 [1, 2, 3, 4]
```

- 集合运算

```javascript
// 取并集
const set1 = new Set([1, 2, 3]);
const set2 = new Set([3, 4, 5]);
const union = new Set([...set1, ...set2]);
console.log(union); // 输出 Set { 1, 2, 3, 4, 5 }

// 取交集
const intersection = new Set([...set1].filter(x => set2.has(x)));
console.log(intersection); // 输出 Set { 3 }

// 取差集
const difference = new Set([...set1].filter(x => !set2.has(x)));
console.log(difference); // 输出 Set { 1, 2 }
```



#### 8.2 Map 映射

Map 存储的是键值对，具有以下特征：

- **键值对存储**：`Map` 存储的是键值对（key-value pairs），**键可以是任意类型且==唯一==**
- **顺序性**：`Map` 会记住键值对的插入顺序。

##### 8.2.1 创建 Map

```javascript
const map = new Map(); // 创建一个空映射

const fruits = new Map([
  ["apples", 500],
  ["bananas", 300],
  ["oranges", 200]
]);
```

##### 8.2.2 Map 方法

- `map.set(key, value)`：向 Map 映射**添加或更新**键值对

```javascript
// 创建一个 Map
const fruits = new Map();

// 设置 Map 的值
fruits.set("apples", 500);
fruits.set("bananas", 300);
fruits.set("oranges", 200);

// 更改已存在的 Map 映射
fruits.set("apples", 5000);
```

- `map.get(key)`：根据键获取对应的值。如果键不存在，返回 `undefined`

```javascript
console.log(fruits.get('apples')); // 输出 5000
console.log(fruits.get('watermelon'));  // 输出 undefined
```

- map.has(key)：判断 Map 是否存在指定的键 key，如果存在则返回 true

> 注意：如果键是对象或者数组，则比较的是**引用地址**，即使内容一样也不行

```javascript
console.log(fruits.has('apples')); // 输出 true
console.log(fruits.has('watermelon'));  // 输出 false
```

- map.delete(key)：删除 Map 中指定的键值对，删除成功则返回 true

```javascript
fruits.delete('apples'); // 删除键为 'apples' 的键值对
```

- `map.clear()`：清空 `Map` 中的所有键值对
- `map.size()`：获取当前 Map 的键值对数量

##### 8.2.3 Map 遍历

- `map.keys()`：返回一个包含**所有键的迭代器**

```javascript
const keys = map.keys()
for (const key of keys) {
 console.log(key); 
}
```

- `map.values()`：返回一个包含**所有值的迭代器**

```javascript
const values = map.values()
for (const value of values) {
 console.log(value); 
}
```

- `map.entries()`：返回一个包含所有**键值对的迭代器**

```javascript
const entries = map.entries()
for (const [key, value] of entries) {
 console.log(key, value); 
}
```

- **`map.forEach()`：遍历 `Map` 中的每个键值对**

```javascript
map.forEach((value, key) => {
  console.log(key, value);
});
```

#### 8.3 Set 和 Map 区别

| 特性           | Set              | Map                       |
| -------------- | ---------------- | ------------------------- |
| **存储内容**   | 唯一值（values） | 键值对（key-value pairs） |
| **键的类型**   | 无键，只有值     | 任意类型                  |
| **值的唯一性** | 值唯一           | 键唯一，值可以重复        |
| **常用方法**   | add、has、delete | set、get、has、delete     |
| **应用场景**   | 存储唯一值，去重 | 存储键值对，快速查找      |



### 9. 浅拷贝和深拷贝

#### 9.1浅拷贝：只复制对象的**第一层**属性

* 基本类型 --> 复制值
* **引用类型 --> 复制内存地址（共享同一块内存地址）**而不复制值

##### 浅拷贝的实现方式：

- 直接赋值

```javascript
var stu = {}
var stu1 = stu
```

- `Object.assign(target, origin)`：

```javascript
const original = { a: 1, b: { c: 2 } };
const copy = Object.assign({}, original);

copy.a = 10;       // 修改第一层 → 不影响原对象
copy.b.c = 20;     // 修改嵌套对象 → 原对象跟着变！

console.log(original); // { a: 1, b: { c: 20 } }
```

- `扩展运算符 ...`

```javascript
const original = [1, 2, { d: 3 }];
const copy = [...original];

copy[0] = 10;      // 修改第一层 → 原数组不变
copy[2].d = 30;    // 修改嵌套对象 → 原数组跟着变！

console.log(original); // [1, 2, { d: 30 }]
```

- 数组的 `slice()`

```javascript
const original = [1, 2, { e: 4 }];
const copy = original.slice();

copy[0] = 10;      // 原数组不变
copy[2].e = 40;    // 原数组的 e 变为 40
```

- 数组的 `concat()`

```javascript
const original = [1, 2, { f: 5 }];
const copy = original.concat();

copy[0] = 10;      // 原数组不变
copy[2].f = 50;    // 原数组的 f 变为 50
```

#### 9.2 深拷贝：递归复制对象的**所有层级**属性，创建**==完全独立==的副本**

* 无论基本类型还是引用类型 --> 全部复制值
* **新对象与原对象彻底隔离，互不影响**
* ==深拷贝需要注意**循环引用**的问题！！！==

##### 深拷贝的实现方式：

- ` JSON.parse(JSON.stringify())`

```javascript
const deepCopy = JSON.parse(JSON.stringify(obj));
```

- **`for ... in ...` 递归**

```javascript
function deepClone(obj, cache = new WeakMap()) {
  // 检查是否是基本类型或 null
  if (typeof obj !== 'object' || obj === null) return obj;
  // 解决循环引用 --> 一旦再次遇到同一个对象，就直接返回之前拷贝的结果
  if (cache.has(obj)) return cache.get(obj);
    
  //  处理 Date 和 RegExp
  if (obj instanceof Date) { return new Date(obj) }
  if (obj instanceof RegExp) { return new RegExp(obj) }
  // 处理数组和对象
  const clone = Array.isArray(obj) ? [] : {};
  // 缓存已拷贝的对象，用于检测循环引用
  cache.set(obj, clone);
  // 递归拷贝
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      clone[key] = deepClone(obj[key], cache);
    }
  }
  return clone;
}


let obj2 = deepClone(obj1);
```

#### 9.3 总结

| 特征                       | 浅拷贝                       | 深拷贝                     |
| -------------------------- | ---------------------------- | -------------------------- |
| **复制层级**               | 仅第一层                     | 所有嵌套层级               |
| **引用类型处理**           | 复制内存地址（共享嵌套对象） | 递归创建新对象（完全独立） |
| **修改嵌套对象影响原对象** | 是                           | 否                         |
| **性能**                   | 高（仅复制一层）             | 低（递归遍历所有层级）     |
| **使用场景**               | 简单数据，无需深层修改       | 复杂嵌套结构，需完全隔离   |



### 10. import 和 requie

#### 10.1 import

`import` 属于 **`ESModule`** 模块，具有以下特征：

- **编译时解析**：模块依赖在代码执行前就确定，**==import 只会执行一次，多次 import 会引用同一实例==**
- **静态**分析：必须在模块顶层，不能在条件语句中使用（动态 import 除外）
- 异步加载：浏览器中是异步加载的，**现代浏览器原生支持**

```javascript
// 导入
import fs from 'fs';
import { readFile } from 'fs';
import * as myModule from './myModule';
import myDefault, { namedExport } from './myModule';

// 导出
export default myModule;
export const myFunction = () => {};
```

#### 10.2 require

reuqire 属于 **`CommonJs`** 模块，具有以下特征：

- **运行时执行**：模块在代码执行到 `require` 语句时才加载，**==每次 require 都会执行模块代码==**
- **动态**加载：可以在条件语句中使用
- 同步加载：在 Node.js 中是同步操作，**Node.js 原生支持的模块加载方式**

```javascript
// 导入
const fs = require('fs');
const { readFile } = require('fs');
const myModule = require('./myModule');

// 导出
module.exports = myModule;
exports.myFunction = () => {};
```



### 11. GET、POST、PUT、DELETE 区别

#### 11.1 GET 方法

- **作用**：**从服务器获取数据**
- **参数位置**：通过 URL 的查询字符串传递参数。
- **请求体**：无请求体。
- **幂等性**：幂等，多次请求结果相同。

> **什么是幂等：像同一资源发送相同的请求，无论执行多少次结果都保持一致。**

#### 11.2 POST 方法

- **作用**：**向服务器提交数据，通常用于创建新资源**
- **参数位置**：数据在请求体中传递。
- **请求体**：有请求体（如表单数据或 JSON）。
- **幂等性**：非幂等，每次请求可能创建新的资源。

#### 11.3 PUT 方法

- **作用**：**创建或更新指定的资源**
- **参数位置**：数据在请求体中传递。
- **请求体**：有请求体（包含完整的资源数据）。
- **幂等性**：幂等，多次请求结果一致。

#### 11.4 DELETE 方法

- **作用**：**从服务器删除指定的资源**
- **参数位置**：资源通过 URL 指定。
- **请求体**：通常无请求体。
- **幂等性**：幂等，多次请求结果相同（资源被删除）。

#### 11.5 总结

| 方法       | **作用**       | **请求体**                    | **幂等性** | **适用场景**   |
| ---------- | -------------- | ----------------------------- | ---------- | -------------- |
| **GET**    | 查询数据       | 参数在 URL 中，查询字符串形式 | 是         | 获取数据       |
| **POST**   | 创建资源       | 有                            | 否         | 创建新资源     |
| **PUT**    | 更新或创建资源 | 有（完整资源数据）            | 是         | 更新或替换资源 |
| **DELETE** | 删除资源       | 一般参数在 URL 中             | 是         | 删除指定资源   |



### 12 Cookie sessionStorage localStorage 的区别

#### 12.1 定义与本质

- `Cookie`：**浏览器与服务器之间自动传输的小型文本数据**，它由服务器通过 HTTP 响应头（`Set-Cookie`）发送给浏览器，之后浏览器会在每次请求同一网站时自动通过 HTTP 请求头（`Cookie`）将其回传给服务器，从而实现**有状态的会话管理**
- `localStorage`：是浏览器提供的一种 **客户端存储机制**（即浏览器本地存储），允许网页在用户的本地浏览器中存储 **持久化键值对数据**，不会自动传给服务器。
- `sessionStorage`：是浏览器提供的另一种 **客户端存储机制**（即浏览器本地存储），一般用于在 **当前浏览器标签页会话期间** 临时存储键值对数据，**仅在当前标签页会话中有效**，关闭即清空。

#### 12.2 存储容量

- `Cookie`：通常不超过4kb。
- `localStorage`：通常大小限制为5MB,不同浏览器存在差异。
- `sessionStorage`：通常大小限制为5MB,不同浏览器存在差异。

#### 12.3 生命周期

- `Cookie`：**会话期 Cookie**的生命周期与浏览器会话关联，一般浏览器关闭就自动删除。**持久性Cookie**的生命周期与`Expires` 或 `Max-Age` 属性设置的过期时间相联系，在过期时间之前持久保存在本地，浏览器关闭也会保存。当到达过期时间浏览器自动删除。
- `localStorage`：永久性的，除非主动删除或用户清理缓存。
- `sessionStorage`：其生命周期与浏览器标签页严格绑定，关闭标签页或窗口即清除，刷新页面仍保留。

#### 12.4 是否参与请求

- `cookie`：自动携带，浏览器会在**每次符合作用域的 HTTP 请求头**中自动附加 Cookie。
- `localstorage`:  **纯前端存储**，数据仅通过 `JavaScript API` 读写，**不参与任何网络通信**。
- `sessionStorage`：与localstorage一样 **纯前端存储**

#### 12.5 安全性

| **安全维度**       | **Cookie** | **localStorage** | **sessionStorage** |
| ------------------ | ---------- | ---------------- | ------------------ |
| **XSS 攻击风险**   | 高风险     | 中高风险         | 中高风险           |
| **CSRF 攻击风险**  | 高风险     | 无风险           | 无风险             |
| **数据持久性风险** | 中风险     | 高风险           | 低风险             |
| **网络传输风险**   | 高风险     | 无风险           | 无风险             |
| **隐私模式泄漏**   | 中风险     | 中风             | 低风险             |

> 引申：XSS 和 CSRF 攻击：
>
> - **XSS攻击：** 跨站脚本攻击。
>   - `攻击者脚本` 嵌入 `被攻击网站`，获取用户cookie等隐私信息。
>     - 首先黑客利用站点漏洞将一段恶意 `JavaScript` 代码提交到网站的数据库中；
>     - 然后用户向网站请求包含了恶意 `JavaScript` 脚本的页面；
>     - 当用户浏览该页面的时候，恶意脚本就会将用户的 `Cookie、Session` 信息等数据上传到服务器
> - **CSRF攻击：** 跨站请求伪造。
>   - `已登录用户` 访问 `攻击者网站`，攻击网站向被攻击网站发起恶意请求（利用浏览器会自动携带cookie）。
>   - **CSRF攻击就是黑客利用了用户的登录状态，并通过第三方的站点来做一些坏事**
>   - 如：将转账的请求接口隐藏在 img 标签内，欺骗浏览器这是一张图片资源。当该页面被加载时，浏览器会自动发起 img 的资源请求，如果服务器没有对该请求做判断的话，那么服务器就会认为该请求是一个转账请求，于是用户账户上的100极客币就被转移到黑客的账户上去了



### 13. Math 函数

- **Math.floor()**：向下取整

- **Math.ceil()**：向上取整
- **Math.round()**：四舍五入取整
- **Math.random()**：返回一个**大于等于0且小于1**的随机浮点数，即：**[0,1)**
- **Math.max()**：返回一组数的最大值
- **Math.min()**：返回一组数的最小值
- **Math.pow(base,index)**：返回**基数(base)**的**指数(index)**次幂
- **Math.sqrt()**：返回一个数的平方根



### 14. 字符串方法

- **`str.length`**：获取字符串长度

- **`str.charAt(index)`**：获取 `index（从0开始）` 位置处的字符，超出索引返回 `''`

- **`str.indexOf(value, start)`**：从 `start（默认为0）` 开始查找 `value` 第一次出现的位置，找不到返回 -1

- **`str.lastIndexOf(value)`**：查找 `value ` 最后一次出现的位置，找不到返回  -1

- **`str.includes(value, start)`**：从 `start（默认为0）` 开始查找是否包含指定的子字符串 `value` ，找到返回 `true`

- **`str.concat(str1, str2...)`**：将多个字符串拼接为新的字符串，并返回新的字符串

- **`str.split(separator,limit)`** ：将字符串分割成字符串数组

  - `separator`：必需。字符串或正则表达式，从该参数指定的地方分割 string。
  - `limit`：可选。该参数可指定返回的数组的最大长度。如果设置了该参数，返回的子串不会多于这个参数指定的数组。如果没有设置该参数，整个字符串都会被分割，不考虑它的长度。

  ```javascript
  let str = "abcdef";
  str.split("c");    // 输出结果：["ab", "def"]
  str.split("", 4)   // 输出结果：['a', 'b', 'c', 'd'] 
  ```

- **`str.slice(start, end)`**: 返回字符串从 `start ~ end-1` 的字符串

  - 如果 `start` 为负数，则从尾部开始
  - 如果不传 `end` 则默认为 `length -1`

- **`str.substr(start,length)`**：从下标 `start` 开始截取长度为 `length` 的字符串

  - 如果 `start` 为负数，则从尾部开始
  - 如果不传 `length` 则默认截取到字符串末尾

  ```javascript
  let str = "abcdefg";
  str.substr(1,6); // 输出结果："bcdefg" 
  str.substr(1);   // 输出结果："bcdefg" 相当于截取[1,str.length-1]
  str.substr();    // 输出结果："abcdefg" 相当于截取[0,str.length-1]
  str.substr(-1);  // 输出结果："g"
  ```

- **`str.substring(from, to)`**：提取字符串 `form ~ to -1 ` 下标之间的字符

  - `form/to` 均非负，如果参数为负数，则返回这个字符串
  - 如果参数 `from` 和 `to` 相等，那么该方法返回的就是一个空串（即长度为 0 的字符串）
  - 如果 `from` 比 `to` 大，那么该方法在提取子串之前会先交换这两个参数

  ```javascript
  let str = "abcdefg";
  str.substr(1,6); // 输出结果："bcdefg" 
  str.substr(1);   // 输出结果："bcdefg" 相当于截取[1,str.length-1]
  str.substr();    // 输出结果："abcdefg" 相当于截取[0,str.length-1]
  str.substr(-1);  // 输出结果："g"
  ```

- **`str.toLowerCase()/str.toUpperCase()`**：将字符串的大小写转换

- **`str.replace(oldvalue, newvalue)`**：将字符串中的 `oldvalue` 替换为 `newvalue`

- **`str.search(value)`**：检索字符串中是否存在子字符串 `value`，返回字符串中第一个与 `regexp` 相匹配的子串的起始位置

- **`str.trim()`**：去除字符串两边的空格

- **`str.padStart(length, value)`**：将字符串从头开始用 `value` 填充为长度为 `length` 的新字符串

- **`parseInt(string, radix)`**：将字符串 `str` 按照进制 `radix` 解析成整数

  - 十进制解析（默认）

  ```javascript
  parseInt("42");    // 输出 42
  parseInt("042");   // 输出 42
  ```

  -  十六进制解析：字符串以 `0x` 或 `0X` 开头

  ```javascript
  parseInt("0x1F");  // 输出 31（十六进制 1F 对应十进制 31）
  parseInt("0xFF");  // 输出 255（十六进制 FF 对应十进制 255）
  ```

  - 二进制解析：字符串以 `0b` 或 `0B` 开头

  ```javascript
  parseInt("0b1010");  // 输出 10（二进制 1010 对应十进制 10）
  parseInt("0B1010");  // 输出 10（二进制 1010 对应十进制 10）
  ```

  - 自定义进制：指定 `radix` 

  ```javascript
  parseInt("1010", 2);   // 输出 10（二进制 1010 对应十进制 10）
  parseInt("A1", 16);     // 输出 161（十六进制 A1 对应十进制 161）
  parseInt("10", 8);      // 输出 8（八进制 10 对应十进制 8）
  parseInt("10", 5);      // 输出 5（五进制 10 对应十进制 5）
  parseInt("ZZ", 36);     // 输出 1295（三十六进制 ZZ 对应十进制 1295）
  ```

  - 字符串的**开头不是有效的数字**或者字符串**包含无法转换为数字的字符**会输出 `NaN`

  ```javascript
  parseInt("abc");  // 输出 NaN
  parseInt("1a2b3c");  // 输出 1
  ```

  - 字符串**末尾存在无效字符会自动忽略**

  ```javascript
  parseInt("42px");  // 输出 42
  parseInt("100abc");  // 输出 100
  ```

  

### 15. 
