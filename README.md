## `ES6~ES10`

### `let & const`：

#### `1-1`  变量提升：

在函数作用域或全局作用域中通过关键词var声明的变量，无论实际上是在哪里声明的，都会被当成在当前作用域顶部声明的变量。

```javascript
function getValue(condition){
    if(condition){

        var result="blue";

        return result;

    }else{

        //此处可以访问到变量result  其值为undefined

        return null;

    }

    //此处也可以访问到变量result 其值为undefined

}
```

上述代码其实经过浏览器解析后，会存在变量提升的状况，真正的解析为：

```javascript
function getValue(condition){
    var result;
    if(condition){
        result="blue";
        return result;        
    }else{
        return null;
    }
}
```

#### `1-2`  块级声明：

块级声明用于声明在指定块的作用域之外无法访问的变量。块级作用域存在于：

- 函数内部：

- 块中（字符{和}之间的区域）：

#### `1-3`   `let`声明：

`let`声明的用法和`var`相同，用`let`代替`var`来声明变量，就可以把变量的作用域限制在当前代码块中。

`let`声明只能声明一次，禁止重复声明。

```javascript
var count=30;
let count=40;
// Uncaught SyntaxError: Identifier 'count' has already been declared
```

#### `1-4`  `const`声明：

使用`const`声明的是常量，其值一旦被设定后不能被再次修改，而且每一个通过`const`声明的变量必须进行初始化。

```javascript
const pi=3.14;
pi=314;
// Uncaught TypeError: Assignment to constant variable.
```

必须进行初始化：

```javascript
const pi;
pi=3.14;
// Uncaught SyntaxError: Missing initializer in const declaration
```

`const`不允许修改绑定，但是允许修改值，这就意味着可以修改`const`定义的对象的属性值。

#### `1-5`  循环中的块作用域：

在循环函数中使用`var`定义变量，循环结束后，仍然能访问到该变量。

```javascript
for(var i=0;i<10;i++){
    console.log(i);// 0 ... 9    
}
console.log(i);//10
```

#### `1-6`   全局块作用域：

`let`和`const`与`var`的另外一个区别是它们在全局作用域中的行为。当`var` 被用于全局作用域时，它会创建一个新的全局变量作为全局对象（浏览器中的`window`对象）的属性。这就意味着`var`很有可能会无意中覆盖一个已经存在的全局属性。

```javascript
var _name1="cowen";
let _name2="zheng";
window._name1;//cowen
window._name2;//undefined
```



### 函数

#### `2-1`、默认参数：

在`ECMAScript5`中，我们可以通过下列方式创建函数并模拟函数默认值：

```javascript
function fn(a,b){
    var a=a || 100,

        b=b || 200;

    return a+b;

};
fn();// 300
fn(200,300);// 500
```

ES6简化了函数默认值的设置，只需要给形参赋值即可完成默认值的设置。

```javascript
function fn(a=100,b=200){
    return a+b;
}
fn();//300
fn(200,300);//500
```

- 默认参数值对`arguments`对象的影响：

  在ES5非严格模式下，`arguments`的值会随着形参的变化而变化，而且`arguments`的长度即为形参的个数。

```javascript
function fn(a,b){
    arguments.length;//2
    arguments[0]===a;//true
    arguments[1]===b;//true
    a="c";
    b="d";
    arguments.length;//2
    arguments[0]===a;//true
    arguments[1]===b;//true
}
fn("a","b");
```

但是在严格模式下，`arguments`的值不会随着形参的变化而变化。

```javascript
function fn(a,b){
    "use strict";
    arguments.length;//2
    arguments[0]===a;//true
    arguments[1]===b;//true
    a="c";
    b="d";
    arguments.length;//2
    arguments[0]===a;//false
    arguments[1]===b;//false
}
fn("a","b");
```

在ES6中，无论严格模式还是非严格模式，`arguments`的值都不会随着形参的改变而改变，而且默认参数不会被记录到形参`arguments`中。

```javascript
function fn(a,b="b"){
    arguments.length;// 1
    arguments[0]===a;//true
    arguments[1]===b;//false
};
fn("A");
```

#### `2-2`、不定参数：

在函数的命名参数前添加三个点`...`就表明这是一个不定参数，该参数为一个数组，包含着自它之后传入的所有参数，通过这个数组可以一一遍历里面的参数。

```javascript
function pick(obj,...keys){
    let result=Object.create(null);
    let len=keys.length;
    for(let i=0;i<len;i++){
        result[keys[i]]=object[keys[i]];        
    }
    return result;
}
let result=pick({name:"cowen",age:32},"name","age","sex");
result;// {name: "cowen", age: 32, sex: undefined}
```

> Note：函数的`length`属性统计的是函数命名参数的数量，不定参数的加入不会影响`length`属性的值。

不定参数的使用也是有限制的。首先，每个函数最多只能声明一个不定参数，而且一定要放在所有参数的末尾。其次，不定参数不能用于`setter`之中。

不定参数对`arguments`对象是没有影响的，无论传入多少个参数，`arguments`对象总会和不定参数想对应。

```javascript
function pick(...keys){
    arguments[0] === keys[0];//true
    arguments[1] === key[1];//true
}
```

#### `2-3`、`name`属性：

`ES6`中为所有函数新增了name属性。

```javascript
function fn(){
    //...
}
var Fn=function(){
    //...
}
fn.name;//fn
Fn.name;//Fn
```

#### `2-4`、函数的多重用途：

`ECMAScript5`及早期版本中的函数具有多重功能，可以结合`new`使用，函数内的`this`值将指向一个新对象，函数最终会返回这个新对象。

```javascript
function Person(name){
    this.name=name;
}
var XiaoMing=new Person("小明");
var DaMao=Person("大毛");
XiaoMing;//[Object object]
DaMao;//"undefined"
```

`JavaScript`函数有两个不同的内部方法：`[[Call]]`和`[[Construct]]`，当`new`关键字调用函数时，执行的是构造函数，它负责创建一个通常被称作实例的新对象，然后再执行函数体，将`this`绑定到实例上；如果不通过`new`关键字调用函数，则执行`[[Call]]`函数，从而直接执行代码中的函数体。

为了强制使得构造函数使用`new`关键字，我们可以使用`instanceof`判断，但是这样无法绕过`call/apply`修改`this`绑定的修改。

```javascript
function Person(name){
    if(this instanceof Person){
        this.name=name;        
    }else{
        throw new Error("必须通过new关键字来调用Person");
    }
}
var XiaoMing=new Person("小明");
var SanMao=Person("Tim");//抛出错误
var DaMing=Person.call(XiaoMing,"DaMing");//undefined
```

为了判断函数是否通过`new`关键字调用的问题，`ES6`引入了`new.target`这个元属性。元属性是指非对象的属性，其可以提供非对象目标的补充信息，当调用函数的构造函数时，`new target`被赋值为`new`操作符的目标，通常是新创建对象的实例。

有了元属性的存在，可以通过检查`new.target`是否被定义过来安全地检测一个函数是否通过`new`关键字调用的。

```javascript
function Person(name){
    if(typeof new.target!=="undefined"){
        this.name=name;        
    }else{
        throw new Error("必须通过new关键字来调用Person");
    }
}
var XiaoMing=new Person("xiaoming");
var DaMing=Person.call(XiaoMing,"DaMing");//抛出错误
var SanMao=Person("sanmao");//抛出错误
```

#### `2-5`、块级函数：

#### `2-6`、箭头函数：

箭头函数是`ECMAScript6`中最有趣的新增属性，它有传统的`JavaScript`函数有些不同，主要集中在以下几个方面：

- 没有`this`、`super`、`arguments`和`new.target`绑定：箭头函数的这些值都是有最近一层非箭头函数决定。

- 不能通过`new`关键字调用：箭头函数没有`[[Construct]]`方法，所以不能被用作构造函数，如果通过`new`构造函数会抛出错误。

- 没有原型：没有原型，即不存在`prototype`这个属性。

- 不可以修改`this`的绑定：函数内部的`this`值不可以被修改，在函数的生命周期内始终保持一致。

- 不支持`arguments`对象：箭头函数没有`arguments`绑定。

- 不支持重复的命名参数：不支持重复命名的函数形参。

箭头函数的语法多样，常见的包括空函数和返回值函数。

```javascript
//空函数定义
let fn=()=>{};
//返回值函数定义
let fn=(key)=>(key);
let fn=(key)=>key;
//类似于：
let fn=function(key){
    return key;
}
```

> Note：如果函数返回的是一个变量的话，则可以使用返回值带不带括号都可以，但是如果返回值是一个对象的话则必须加上`()`。

箭头函数的`this`指向是根据上下文来决定的，如果箭头函数被非箭头函数包含，则`this`绑定的是最近一层非箭头函数的`this`；否则，`this`的值会被设置为全局对象。
