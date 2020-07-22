# 一. JS存储
**1. 基本类型和引用类型如何存储的？堆和栈有什么区别？**
基本类型存储在栈中，引用类型存储在堆中，具体如图：
![](https://pic2.zhimg.com/80/v2-739302e3f0cde0e831d75be8d8ade09a_1440w.png)

![](https://pic2.zhimg.com/80/v2-b9e359bce8bd7424732aa9999d1caf4a_1440w.png)

**2. 栈内存和堆内存的优缺点**
在JS中，基本数据类型变量大小固定，并且操作简单容易，所以把它们放入栈中存储。
引用类型变量大小不固定，所以把它们分配给堆中，让他们申请空间的时候自己确定大小，这样把它们分开存储能够使得程序运行起来占用的内存最小。
栈内存由于它的特点，所以它的系统效率较高。
堆内存需要分配空间和地址，还要把地址存到栈中，所以效率低于栈。

**3. 栈内存和堆内存的垃圾回收**
栈内存中变量一般在它的当前执行环境结束就会被销毁被垃圾回收制回收， 而堆内存中的变量则不会，因为不确定其他的地方是不是还有一些对它的引用。 堆内存中的变量只有在所有对它的引用都结束的时候才会被回收。

# 二.原型链、闭包
### **1、原型链**
**原型对象**
要想让构造函数生成的所有实例对象都能够共享属性，那么我们就给构造函数加一个属性叫做prototype，用来指向原型对象，我们把所有实例对象共享的属性和方法都放在这个构造函数的prototype属性指向的原型对象中，不需要共享的属性和方法放在构造函数中。

**原型继承**
原型链是实现原型继承的主要方法，基本思想就是利用原型让一个引用类型继承另一个引用类型的属性和方法。

**原型链**
既然每个对象都有一个_proto_属性指向原型对象，那么原型对象也有_proto_指向原型对象的原型对象，直到指向Object原型的null，这才到达原型链的顶端。

**判断一个变量是数组的方法：**
`[1,2] instanceof Array`
`[1,2].__proto__ == Array.prototype`
`Array.prototype.isPrototypeOf([1,2])`

**new一个实例过程**
用伪代码模拟其内部流程如下：
```javascript
newAnimal('cat') = {
    varobj = {};
    obj.__proto__ = Animal.prototype;
    varresult = Animal.call(obj,"cat");
    returntypeofresult === 'object'? result : obj;
}
```
将上述流程分为 4 个步骤来理解：
（1） 创建一个空对象 obj；
（2） 把 obj 的__proto__ 指向构造函数 Animal 的原型对象 prototype，此时便建立了 obj 对象的原型链：obj->Animal.prototype->Object.prototype->null
（3） 在 obj 对象的执行环境调用 Animal 函数并传递参数 “ cat ” 。
（4） 考察第 3 步的返回值，如果无返回值 或者 返回一个非对象值，则将 obj 作为新对象返回；否则会将 result 作为新对象返回。

**原型链使用场景**
1）使用原型对象扩展自定义对象
`Array.prototype.isFirst = () => {}`


### **2、闭包**
**变量的作用域**：简单来说，作用域 指程序中定义变量的区域，它决定了当前执行代码对变量的访问权限。分为全局变量和局部变量。
**变量提升：**
先来看看什么是JavaScript中的声明和赋值
```var myname = '极客时间'```
这段代码你可以把它看成是两行代码组成的：
```
var myname    //声明部分
myname = '极客时间'  //赋值部分
```
**所谓的变量提升，是指在JavaScript代码执行过程中，JavaScript引擎把变量的声明部分和函数的声明部分提升到代码开头的“行为”。变量被提升后，会给变量设置默认值，这个默认值就是我们熟悉的undefined。**

**JavaScript代码的执行流程**
**1）编译阶段**
第一部分：变量提升部分的代码。
```
var myname = undefined
function showName() {
    console.log('函数showName被执行');
	```
}
第二部分：执行部分的代码。

```showName()
console.log(myname)
myname = '极客时间'
```
我们可以一行一行来分析上述代码：

- 第1行和第2行，由于这两行代码不是声明操作，所以JavaScript引擎不会做任何处理；
- 第3行，由于这行是经过var声明的，因此JavaScript引擎将在环境对象中创建一个名为myname的属性，并使用undefined对其初始化；
- 第4行，JavaScript引擎发现了一个通过function定义的函数，所以它将函数定义存储到堆(HEAP）中，并在环境对象中创建一个showName的属性，然后将该属性值指向堆中函数的位置（不了解堆也没关系，JavaScript的执行堆和执行栈我会在后续文章中介绍）。 这样就生成了变量环境对象。接下来JavaScript引擎会把声明以外的代码编译为字节码
![](https://camo.githubusercontent.com/0b69270ff3ea627e6752f0c0a4824a5790e087d5/68747470733a2f2f7374617469633030312e6765656b62616e672e6f72672f7265736f757263652f696d6167652f30362f31332f30363535643138656333343761393564666266383433393639613932316131332e706e67)
**2) 执行阶段**
JavaScript引擎开始执行“可执行代码”，按照顺序一行一行地执行。下面我们就来一行一行分析下这个执行过程

- 当执行到showName函数时，JavaScript引擎便开始在变量环境对象中查找该函数，由于变量环境对象中存在该函数的引用，所以JavaScript引擎便开始执行该函数，并输出“函数showName被执行”结果。
- 接下来打印“myname”信息，JavaScript引擎继续在变量环境对象中查找该对象，由于变量环境存在myname变量，并且其值为undefined，所以这时候就输出undefined。
- 接下来执行第3行，把“极客时间”赋给myname变量，赋值后变量环境中的myname属性值改变为“极客时间”

**什么是闭包？闭包为了解决什么问题？为什么闭包不会被回收？**
**闭包概念：**任何持有外部引用的函数，在定义完成之后都是一个闭包。这和在函数内部定义的局部变量不同，局部变量会在函数执行的时候创建、赋值，执行结束之后随着执行上下文销毁。而所持有的外部变量则会因为附着在[[scope]]（作用域链）上而被保留。
**闭包的用途：**它的最大用处有两个，一个是前面提到的可以读取函数内部的变量，另一个就是让这些变量的值始终保持在内存中。

闭包相当于函数的上下文（或者执行环境），编译器通过语法分析，聪明的判断哪些数据需要自动“打包”到函数的上下文里面，通过闭包自动替你管理变量的作用域和生命周期，程序员拿来即用，用完即走，带来丝般顺滑的开发体验。

**闭包主要是为了解决什么问题？**
在比较纯的函数式语言中，还没有对象时，闭包一般代替对象，将各种相关的数据封装起来。考虑到函数式语言是最早采用闭包的，应该是当时遇到同样的问题（封装数据，限制修改，保证数据一致性），有些语言选择了对象，函数式语言选择了闭包吧。此时闭包就成了函数式语言中的封装器，起到对象的作用。

**为什么闭包不会被回收？**
如果闭包函数被外部变量接收，那么这个存在于堆内存中的闭包函数对象就一直存在着（因为它被外部引用着，只要外部作用域不退出，就不会被垃圾回收清除）。那么这个函数对象上的 [[Scope]] 属性（即作用域链）自然就不会被清除，那么作用域链引用的所有层级包含函数的活动对象就不会被清除。

**闭包应用场景**
1） setTimeout
`setTimeout(f1,1000);`
2)函数防抖
`window.onresize = function(){
debounce(fn,1000)
}`
3）回调
4) 用闭包定义能访问私有函数和私有变量的公有函数。
5) 节点循环绑定click事件
```javascript
for (var i = 0; i < infoArr.length; i++) {
    	var item = infoArr[i];
    	document.getElementById(item.id).onfocus = function(){
    		showContent(item.content)
    	}
    }
```
# 3.es6
1）模板字符串实现原理： 通过正则匹配，替换原字符串中的变量
2）对象和数组解构赋值原理： 解构是ES6提供的语法糖，其实内在是针对可迭代对象的Iterator接口，通过遍历器按顺序获取对应的值进行赋值。
3）for of和for in的区别
for in遍历的是数组的索引（即键名），而for of遍历的是数组元素值。 所以for in更适合遍历对象，不要使用for in遍历数组。
4)数组扩展方法
- 通过for...of结构，允许多重循环。注：新数组会立即在内存中生成，这时如果原数组是一个很大的数组，将会非常耗费内存。
```
var a1 = [1, 2, 3, 4];
var a2 = [for (i of a1) i * 2];
```
- Array.from()：Array.from()：用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象，其中包括ES6新增的Set和Map结构。Array.from()还可以接受第二个参数，作用类似于数组的map方法，用来对每个元素进行处理。
`Array.from({ 0: "a", 1: "b", 2: "c", length: 3 });`
`Array.from(arrayLike, x => x * x);`
- Array.of()方法用于将一组值，转换为数组。弥补数组构造函数Array()的不足。
`Array.of(3, 11, 8) // [3,11,8]`
- 数组实例的find()用于找出第一个符合条件的数组元素；数组实例的findIndex()用于返回第一个符合条件的数组元素的位置。
- 数组实例的fill()使用给定值，填充一个数组。
5)对象扩展
- Object.is()和Object.assign()
Object.is()：用来比较两个值是否严格相等。它与严格比较运算符（===）的行为基本一致，不同之处只有两个：一是+0不等于-0，二是NaN等于自身。

Object.assign()：用来将源对象（source）的所有可枚举属性，复制到目标对象（target）。它至少需要两个对象作为参数，第一个参数是目标对象，后面的参数都是源对象。如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性。
6）Set和Map数据结构
Set结构类似于数组，但是成员的值都是唯一的，没有重复的值。
Map结构类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。
7）promise,Generator ,yeild await区别
Promise 是一个对象，从它可以获取异步操作的消息。Promise对象代表一个异步操作，有三种状态：pending（进行中）、fulfilled（已成功）和rejected（已失败）。
Generator函数是一种可以中途暂停、异步执行的函数
简单的说async函数就相当于自执行的Generator函数，相当于自带一个状态机，在await的部分等待返回， 返回后自动执行下一步。而且相较于Promise,async的优越性就是把每次异步返回的结果从then中拿到最外层的方法中，不需要链式调用，只要用同步的写法就可以了。更加直观而且，更适合处理并发调用的问题。
8）module，与CommonJS 模块有什么区别

ES6 模块跟 CommonJS 模块的不同，主要有以下两个方面：
ES6 模块输出的是值的引用，输出接口动态绑定，而 CommonJS 输出的是值的拷贝，所以原模块更新后，引用进来的模块并不会更新
ES6 模块编译时执行，而 CommonJS 模块总是在运行时加载
```
// es6  import 是静态执行虽然 a 模块中 import 引入晚于 console.log('a')，但是它被 JS 引擎通过静态分析，提到模块执行的最前面,优于模块中的其他部分的执行
// a.js 
console.log('a.js')
import { foo } from './b';

// export 声明的变量也是优于模块其它内容的执行的，但是具体对变量赋值需要等到执行到相应代码的时候
// b.js
export let foo = 1;
console.log('b.js 先执行');

// 执行结果:
// b.js 先执行
// a.js
```
**CommonJS 模块循环依赖**
```
// a.js
console.log('a starting');
exports.done = false;
const b = require('./b');
console.log('in a, b.done =', b.done);
exports.done = true;
console.log('a done');

// b.js
console.log('b starting');
exports.done = false;
const a = require('./a');
console.log('in b, a.done =', a.done);
exports.done = true;
console.log('b done');

// node a.js
// 执行结果：
// a starting
// b starting
// in b, a.done = false
// b done
// in a, b.done = true
// a done
```

**ES6 模块循环依赖**
```
// a.js
console.log('a starting')
import {foo} from './b';
console.log('in b, foo:', foo);
export const bar = 2;
console.log('a done');

// b.js
console.log('b starting');
import {bar} from './a';
export const foo = 'foo';
console.log('in a, bar:', bar);
setTimeout(() => {
  console.log('in a, setTimeout bar:', bar);
})
console.log('b done');

// babel-node a.js
// 执行结果：
// b starting
// in a, bar: undefined
// b done
// a starting
// in b, foo: foo
// a done
// in a, setTimeout bar: 2
```
# 4.异步机制
![](https://uploadfiles.nowcoder.com/files/20190729/595399536_1564330373356_9374643-598ff6c029970ff9.png)

1). 所有同步任务都在主线程上执行，形成一个执行栈(execution context stack)
2). 主线程之外，还存在一个任务队列（task queue），只要异步任务有了运行结果，就在“任务队列”之中放置一个事件。
3). 一旦“执行栈”中的所有同步任务执行完毕，系统就会读取“任务队列”，看看里面有哪些事件。那些对应的异步任务，就结束等待状态，进入执行栈开始被执行。


