# js

* 解构
  * 把赋值赋给赋值左边的变量，注意非对象解构看可以嵌套，可以有多个标识符，对象类型不行。`const { a ,b } = { a : 1 , b : 3}`
  * 不可解构不可迭代类型
  * 通过const和let来决定绑定元素，const就是常量。
  * 剩余属性
    * 在非对象中可以嵌套，但要保证在数组或其他属性的最后边，方便编写，`...rest`
    * 在对象中只能出现一次，且只有一个标识符。
  * 当没有声明时，需要使用小括号来括住，然后再括号前加上分号防止与上一句有关系。
* 在未声明的变量会变成暂时的全局变量，javascript允许对未声明的变量赋值，在严格模式慎用。（禁止使用）
* 没有初始化会变成undefined。
* const禁止未初始化（一般是程序员的错误）因为不允许后续修改。
* 作用域
  * var定义的是全局作用域（当前函数作用域）
  * let和const定义的是块作用域，{}
  * 变量提升
    * 用var生命声明的变量会有提升效果，即变量在声明之前也能被引用，实际上提升的只有该变量的声明和默认初始化。
    * 所以var定义的变量尽量往函数顶端的地方
    * let 和 const 是否被提升是个定义争论。在变量声明之前引用块中的变量，将总是抛出 ReferenceError，因为该变量位于从块的开始到声明所在的“暂时性死区”中。
    * 函数被全部提升
* const仅阻止重新赋值，不阻止修改，这点在对象上体现的多。同样，数组的元素也是不受保护的。
* 字符串转化为数字
  * 可以用`parseInt("101",2)//5`来操作，也有`parseFloat()`
  * 也可以在字符串前直接加上+（一元加运算符）
* js的一个强大之处就在于它可以在函数之内创造函数

```js
function createFunction() {
  function f(a, b) {
    const sum = a + b;
    return sum;
  }
  return f;
}
const f = createFunction();
console.log(f(3, 4)); // 7
```

* 箭头语法

```js
const f = (a, b) => {
  const sum = a + b;
  return sum;
};
console.log(f(3, 4)); // 7
```

***很奇怪，js里的函数很多都像是嵌套一样，是遵循函数式编程的结果，不修改原函数，返回新函数***

* js里函数是不能够重载的，cpp和java是可以的，js里面也是只能传值，不能够传引用和指针，js里面没有默认参数，所以不能跳过，即`f(a,,b)`这种写法是错误的。
* 闭包不单单是变量的价值范围的简单说明，闭包还可以实现改变外部变量，即使外部函数已经调用结束，变量依旧没有销毁，像是一个有记忆的函数，通过嵌套函数来实现的，如下所示：

```js
var createCounter = function(n) {
  let currentCount = n - 1;
  return function() {
    currentCount += 1;
    return currentCount;      
  };
};
```

* 在使用里面的函数时，仍然使用的是currentCount的值，没有因为重新初始化currentCount，也就是说currentCount没有销毁。
* javascript对象和有限方法链
  * 在js里，可以通过返回一个对象的方式来进行有限方法链，即`arr.sort().reverse().join("-")`的方式了连续调用
* 错误处理
  * 通过throw和try...catch来实现,在js里抛出一个字符串，将在catch中被捕获为错误消息

```js
function checkName(name) {
  if (name === '') {
    throw "Name can't be empty!";
  }
  return name;
}

try {
  console.log(checkName(''));
} catch (error) {
  console.error(error); // "Name can't be empty!"
}

let error1 = new Error("First Error");
let error2 = new Error("Second Error");

---

//抛出多个错误，并且Error实例化
try {
  throw new AggregateError([error1, error2], "Two errors occurred.");
} catch (error) {
  if (error instanceof AggregateError) {
    console.error(error.message); // "Two errors occurred."
    for (let e of error.errors) {
      console.error(e.message); // 打印 "First Error" 和 "Second Error"
    }
  }
}

```

* finally块
  * 包含的语句在try和catch块执行后执行，无论是否抛出异常都会执行。return的东西会覆盖try和catch的return值，也可以覆盖catch的抛出错误。
  * 可以嵌套try和catch语句，如果内部try语句没有对应的catch块，那么他必须有一个finally块，以及在包围的try和catch语句中的catch会被检查是否有处理该异常。
  * 在返回错误信息是，最好使用`console.error()`而不是`console.log()`。
  * 返回的Error对象有name和message属性，name属性会返回Error的错误类型，具体取决于你抛出的错误类型，是Error本身或者它的子类，message会返回抛出的错误内容。
* 循环
  * forEach是数组提供的一个遍历方法，用来对数组里的元素执行一次函数。
    * forEach的回调函数可以接收三个参数`array.forEach((element,index,array)=>{})`其中element是当前遍历到的元素，index是当前元素的索引，array是原数组。
    * forEach是同步函数，不是异步函数，除非抛出异常，否则无法跳出。
  * label语句【break + [label] \ continue + [label]】
    * 用来标记循环的位置，最常用的是在多重嵌套循环里break跳出，指定跳出到哪里。比如标记在一个循环前面，调用后调到这个循环的后面。
    * continue则用来跳到这个循环，在这个循环内继续循环。
  * for in和for of
    * for in遍历的是对象的key（属性名）而for of遍历的是值(value)，不推荐使用for in来遍历数组。
  * 数组中存在空位与undefined
    * 正常的for循环会访问空位，并返回undefined。
    * for of会遍历空位，返回undefined。
    * forEach和各种数组方法（如filter，reduce，some，every）会跳过空位。
    * for in会跳过空位，因为没有属性。
* ==命名冲突，就近原则，内部的优先级最高。==
* js里的this和cpp里的不一样，cpp里面的this主要是指针，指向上一级的类，是固定的，而js里的this更像是一个动态绑定，绑定到谁取决于调用方式。
* 闭包
  * js最强大的属性，js里允许函数嵌套，内部函数具有定义在外部函数中所有的变量和函数（以及外部函数能够访问到的所有变量和函数）的完全访问权限。但是外部不能访问内部，这实际上给内部函数进行了一次封装。
  * 由于内部可以访问到外部函数的作用域，因此当内部函数的生存周期大于外部函数时，外部函数中定义的变量和函数的生存周期都将比内部函数执行的持续时间药厂每当内部函数以某一种方式被任何一个外部函数以外的任何作用域访问时，就会创建闭包。
  * IIFE函数表达式，`const getCode = (function(){...})()`这样的话，假如getcode内部返回一个函数，在调用getcode的时候就会直接调用这个函数，return这个函数return的东西（当然还有可能是一个函数，这也是闭包的重要流程），更像是java或者cpp里的类，通过封装来保护一个私有变量不被外界直接访问或者修改，每创建一个函数，就会有一个全新的闭包环境（？）
  * js里也有默认参数（js里面没有函数重载），有剩余属性。
* 正则表达式（此事在linux系统里亦有记载）
  * 正则表达式，是一种用来匹配字符串模式的规则
  * 第一种字面量写法：`var re = /ab+c/;`
  * 第二种构造函数写法：`var re = new RegExp("ab+c");`
* \==这个符号用的少，因为在判断布尔值的时候，某些可以被正确判断，即`1==true`，有些却不能被正确判断，最好的办法就是使用===，然后使用Boolean来转化为布尔值（注意不能使用new去创建一个Boolean对象，这样的话无论传什么值，都是true。或者直接不使用===，即`if(fn(arr[i],i))`这样的话无论函数返回的是什么，都可以布尔值，正确判断。
* 类
  * js里的类是语法糖（初见这个词的时候我还以为是鱼塘的塘，感觉就像函数栈一种感觉，原来是帮助简化语法的意思，像是给了一口糖吃一样），ES6以前都是使用函数来封装一个类（？），使用函数构造器`MyClass.prototype.myMethod=function(){}`
  * 使用new关键字来创建实例。
  * 类不会声明提升（或者可以被提升，但是有暂时性死区）
  * 在类的构造函数里，this的值指向新创建的实例，可以赋予他新的属性，或者读取已有的属性。