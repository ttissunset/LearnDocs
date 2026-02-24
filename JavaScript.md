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

  

### 15. 迭代器 `Iterator`

#### 15.1 迭代器

##### 15.1.1 定义：==符合迭代器协议的对象==，用于==遍历==访问容器对象

> 注意：默认情况下，一个**对象是不可迭代的**，需要指定要迭代的内容

##### 15.1.2 可迭代协议 `iterable protocol`

- 对象内部必须存在一个 **`next`** 方法、 
- `next` 方法返回一个包含**两个属性**的对象：
  - **`done`**：`Boolean`值，表示当前对象是否迭代完毕
  - **`value`**：本次迭代的值，如果没有则返回 `undefined`，`done` 为 `true` 可省略

```javascript
const nums = [11, 22, 33, 44, 55]

let index = 0
// nums 的迭代器对象
const numIterator = {
	next: function(){
		if(index < nums.length){
		    return { done: false, value: nums[index++] }
		} else {
		    return { done: true }
		}
	}
}

// 生成数组通用迭代器对象
function generateArrayIterator(arr){
    let index = 0
    return {
        next: function(){
            if(index < arr.length){
                return { done: false, value: arr[index++] }
            } else {
                return { done: true }
            }
        }
    }
}
```

#### 15.2 可迭代对象

需要满足以下两点：

- 对象必须包含一个特定的**函数 `[Symbol.iterator]`** 用于实现 `@@Iterable` 方法 （**函数名不许改变！**）
- `[Symbol.iterator]` 函数必须**返回一个迭代器**，用于迭代当前对象

> 注意： `for...of...` 循环 用于**遍历可迭代对象**

```javascript
// infos 是一个可迭代对象
const infos = {
    nums: [11, 22, 33, 44, 55],
    // 迭代对象的 nums 属性
    [Symbol.iterator]: function() {
        let index = 0
        const numIterator = {
            // 箭头函数没有自己的 this，会寻找上层的 this 作为自己的 this
            next:() => {
                if(index < this.nums.length){
                    return { done: false, value: this.nums[index++] }
                } else {
                    return { done: true }
                }
            }
        }
        return numIterator
    }
}

conts iterator = infos[Symbol.iterator]()

--------------------------------------------------------------------------------

// 迭代对象的 键、值、键值对
const infos = {
    name:'haha',
    age: 18,
    
    [Symbol.iterator]: function() {
        let index = 0
        
        // 获取对象所有的 键
        const keys = Object.keys(this)
        // 获取对象所有的 值
        const values = Object.values(this)
        // 获取对象所有的 键值对
        const entries = Object.entries(this)
        
        const iterator = {
            next: function() {
                if(index < keys.length){
                    return { done: false, value: keys[index++] }
                } else {
                    return { done: true }
                }
            }
        }
        return iterator
    }
}

for(const item of infos){
    const key = item
    console.log(key)
}
```

#### 15.3 原生迭代器对象

- `String`
- `Array`
- `Map`
- `Set`
- `arguments`
- `NodeList`

![image-20260224102422969](https://gitee.com/phenylaminopropionic-acid/photo/raw/master/sunbox/image-20260224102422969.png)

#### 15.4 自定义类的迭代

 ```javascript
 class Person(){
     constructor(name, age, friends){
         this.name =  name
         this.age = age
         this.friends = friends
     }
     
     // 为所有 Person 对象实现迭代协议，用于迭代 friends 属性
     [Symbol.iterator](){
         let index = 0
         const iterator = {
             next: () => {
                 if(index < this.friends.length){
                     return { done: false, value: this.friends[index++] }
                 } else {
                     return { done: true }
                 }
             }
         }
         return iterator
     }
 }
 
 const p = new Person("acxia", 22, ['zhong','li','hu'])
 
 for(const item of p){
     console.log(item)
 }
 // 'zhong'
 // 'li'
 // 'hu'
 ```

#### 15.5 迭代器的中断

- `break`
- `return`
- `throw`

```javascript
class Person(){
    constructor(name, age, friends){
        this.name =  name
        this.age = age
        this.friends = friends
    }
    
    // 为所有 Person 对象实现迭代协议，用于迭代 friends 属性
    [Symbol.iterator](){
        let index = 0
        const iterator = {
            next: () => {
                if(index < this.friends.length){
                    return { done: false, value: this.friends[index++] }
                } else {
                    return { done: true }
                }
            },
            // 迭代器中包含一个 return 函数，可以监听迭代器是否执行完毕
            return: () => {
                console.log("监听到迭代器中断了")
                return { done: true }
            }
        }
        return iterator
    }
}

const p = new Person("acxia", 22, ['zhong','li','hu'])

for(const item of p){
    console.log(item)
    if(item === 'li') break
}

```



### 16. 生成器 `Generator`

生成器是一种**函数控制、使用**的方案，他可以让我们更加灵活的控制函数的执行和中断

#### 16.1 生成器函数

- 定义： **`function* fn `** 表示生成器函数
- 生成器函数可以通过  **`yield`** 关键字控制函数的**执行流程**

> 注意：**函数执行遇到 `yield` 时，会中断执行**

- 生成器函数的**返回值是一个 `Generator ` 生成器对象**

> 注意：**要执行生成器函数，需要调用生成器对象的 next 方法**

- **==生成器事实上是一个特殊的迭代器==**

```javascript
// 生成器函数
function* fn(){
    console.log('111')
    
    // 接收生成器函数传递的参数
    const str1 = yield '已输出111'
    console.log('222', str1)
    
    yield '已输出222'
    console.log('333')
    
    yield '已输出333'
    // return 可省略
    return undefined
}

// 生成器对象
const generator = fn()

console.log(generator.next())
// '111'
// { done: false, value: '已输出111'}

// 生成器函数传参
console.log(generator.next("next2"))
// '222', 'next2'
// { done: false, value: '已输出222'}

console.log(generator.next())
// '333'
// { done: false, value: '已输出333'}

console.log(generator.next())
// { done: true, value: undefined }
```

```javascript
// 生成器函数
function* fn(){
    console.log('111')
    
    // 接收生成器函数传递的参数
    const str1 = yield '已输出111'
    console.log('222', str1)
    
    const str2 = yield '已输出222'
    console.log('333', str2)
    
    yield '已输出333'
    // return 可省略
    return undefined
}

// 生成器对象
const generator = fn()

console.log(generator.next())
// '111'
// { done: false, value: '已输出111'}

// --- 通过 return() 函数 提前终止   ---
console.log(generator.return("next2"))
// { done: true, value: 'next2'}

console.log(generator.next("next3"))
// { done: true, value: undefined}

console.log(generator.next("next4"))
// { done: true, value: undefined'}
```

#### 16.2 生成器代替迭代器

```javascript
const nums = [1,2,3,4,5]

function* createArrayIterator(arr){
    for(let i = 0;i < arr.length;i++){
        yield arr[i]
    }
}

const arrIterator = createArrayIterator(nums)
console.log(arrIterator.next()) // { done: false, value: 1}
console.log(arrIterator.next()) // { done: false, value: 2}
console.log(arrIterator.next()) // { done: false, value: 3}
console.log(arrIterator.next()) // { done: false, value: 4}
console.log(arrIterator.next()) // { done: false, value: 5}
console.log(arrIterator.next()) // { done: true, value: undefined}
```

#### 16.3 `yield` 语法糖

- **`yield*`** **用于生产一个可迭代对象，用于每次迭代该可迭代对象，每次迭代其中的一个值**

```javascript
const nums = [1,2,3,4,5]

function* createArrayIterator(arr){
	yield* arr
}

const arrIterator = createArrayIterator(nums)
console.log(arrIterator.next()) // { done: false, value: 1}
console.log(arrIterator.next()) // { done: false, value: 2}
console.log(arrIterator.next()) // { done: false, value: 3}
console.log(arrIterator.next()) // { done: false, value: 4}
console.log(arrIterator.next()) // { done: false, value: 5}
console.log(arrIterator.next()) // { done: true, value: undefined}
```

- 替代自定义可迭代类

```javascript
class Person(){
    constructor(name, age, friends){
        this.name =  name
        this.age = age
        this.friends = friends
    }
    
    // 改造为生成器函数
    *[Symbol.iterator](){
		yield* this.friends
    }
}

const p = new Person("acxia", 22, ['zhong','li','hu'])

for(const item of p){
    console.log(item)
}
// 'zhong'
// 'li'
// 'hu'
```



### 17. 属性描述符

#### 17.1 获取属性描述符

**`Object.getOwnPropertyDescriptor(对象，属性名)`** 用于获取对象中某个属性的属性描述符

- **`value`**：属性的值
- **`writable`**：属性是否可被重写（修改）
- **`enumerable`**：属性是否可被遍历
- **`configurable`**：当前属性的属性描述符是否可被修改
- ==**`get`**：属性读取器 `getter` --> 当读取对象的属性时，相当于调用 `get()`==
- ==**`set`**：属性设置器 `setter` --> 当修改对象的属性时，相当于调用 `set(val)`==

```javascript
let obj = {
    a: 1,
    b: 2
}

const desc = Object.getOwnPropertyDescriptor(obj，'a')
console.log(desc)
// {
// 	   value: 1,  --> a 的值
//     writable: true, --> a 可重写 - 可以修改 a 的值
//     enumerable: true, --> a 可遍历 - 可通过console.log, for.in. 循环,Object.keys 等获取到属性 a
//     configurable: true  --> a 的属性描述符可被修改
// }
```

#### 17.2 设置属性描述符

**`Object.defineProperty(对象，属性名，属性描述符)`**用于修改对象中某个属性的属性描述符

> **如果对象中不存在当前属性，则会为当前对象添加该属性并定义属性描述符**

```javascript
let obj = {
    a: 1,
    b: 2
}

Object.defineProperty(obj,'a',{
    value: 10,
    writable: false
})

console.log(obj.value) // 10
console.log(obj.writable) // false
```

#### 17.3 属性访问器

- 读取对象的某个属性，相当于调用 `get()`
- 设置对象的某个属性，相当于调用 `set()`

```javascript
let obj = {}

Object.defineProperty(obj,'a',{
	get: function() {
        console.log('get')
    	return 123
    },
    set: function(val) {
        console.log('set')
	}
})

obj.a = 3 + 2 --> // set(3+2) --> 输出： set
console.log(obj.a) --> // console.log(get()) -->// 输出：get 123
```

#### 17.4 属性描述符实操

```javascript
class Goods{
    constructor(g){
        // 克隆原始对象
        g = { ...g }
        // 冻结对象，防止外部直接修改对象的属性
        Object.freeze(g)
        
        Object.defineProperty(this, 'data', {
            configurable: false,
            get: function(){
                  return g
            },
            set: function(val){
                throw new Error('data 属性是支付的')
            }
        })
        
        let internalNumValue = 0
		Object.defineProperty(this, 'num', {
            configurable: false,
            get: function(){
                  return internalNumValue
            },
            set: function(val){
                if(typeof val !== 'number'){
                    throw new Error('val 属性必须为数字')
                }
                let temp = parseInt(val)
                if(temp !== val){
                    throw new Error('val 属性必须为整数')
                }
                if(value < 0){
                    throw new Error('val 属性必须大于等于 0')
                }
                internalNumValue = val
            }
        })
        
        
		Object.defineProperty(this, 'totalPrice', {
            get: function(){
                  return this.data.price * this.num
            },
        })
        // 等价于：
        // get totalPrice(){
        // 	   return this.data.price * this.num
        // }
        
        // 密封实例对象，防止用户增加实例对象属性
        Object.seal(this)
    }
}
```

