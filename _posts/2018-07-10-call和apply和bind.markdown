---
layout:     post
title:      "Hello 2018"
subtitle:   " \"Hello World, Hello Blog\""
date:       2018-07-19 19:43:00
author:     "yy"
header-img: "img/2018.png"
catalog: true
tags:
    - 学习
---

> “先让自己变的更好”


## Function.prototype.call()
文档第一句: *While the syntax of this function is almost identical to that of apply(), the fundamental difference is that call() accepts an argument list, while apply() accepts a single array of arguments.*
大概意思就是call和apply的语法几乎是一样的，
只是call接口一个入参列表,like `function.call(this.arg, arg1, arg2, ...)`
而apply只接受一个参数数组like `function.apply(this.arg, [, ...argArray])`
    
###Description
A different ***this*** object can be assigned when calling an existing function. this ***this*** to the current object, the calling object. With call, you can write a method once and then inherit it in another object, without having to rewrite the method for the new object.

这句话意思就是说统一的构造函数可以通过call的方式分配不同的this给这个构造函数,然后在调用者call的这个构造函数当中继承它,
通俗一点就是谁调用了 fn.call 那么fn生出来出来的东西就统统都给继承给他，而且调用方式不变  

```
function fn(name, age) { //统一的构造函数
    this.name = name;
    this.age = age;
}

function fn2(sex, name, age) {
    //把this分配给 fn这个构造函数 并且在当前构造函数中继承new fn();
    fn.call(this, name, age);
    this.sex = sex;
}

let man = new fn2('1', 'yy', '23');

// man { name: 'yy', age:'23', sex: '1' };
```
###进阶用法 anonymous function
```
    let groups = [
        { name : 'yy', age: 23 },
        { name : 'yy2', age: 24}
    ]

    for(let i = 0; i< groups.length; i++ ) {
        (function(i) {
            this.print =  function() {
                console.log(this.name, this.age, i);
            }
            this.print()
        }).call(groups[i], i)
    }
    // yy, 23, 0 
    // yy2, 24, 1
```
### 也可以给一个函数指定特定的this
```
let girfriend = 'myy';
let person = function () {
    console.log(this.name + 'is a man, age ==', this.age);
}
let _person = function () {
    //在没有指定this的情况下，this 会访问更高一级作用域的this
    console.log('my girlfriend name is' ,this.woman);
}

let man = {
    name : yy, age:23
}
person.call(man);
//yyis a man, age == 23
_person.call(); 
//my girlfriend name is myy
```
##Function.prototype.apply()
之前说了 apply和 call的区别是传参的方式不同
function.apply(thisArg, [argsArray])
指的注意的是[argsArray] 不止是 数组，也可以是array-like object 
###array-like object 
Some JavaScript objects, such as the NodeList returned by document.getElementsByTagName() or the arguments object made available within the body of a function, look and behave like arrays on the surface but do not share all of their methods. The arguments object provides a length attribute but does not implement the forEach() method, for example.

大概意思就是长的像数组但是又没有数组特征的一些object，
比如 nodelist , nodeList instanceof HTMLCollection 
再比如 arguments 获取数据的方式和数组一样argument[0] 但是typeof arguments == 'object'
在ECMAScript 5的时候arguments被归属于like-array object ,看来这哥们以前也是array
 ###用法合并一个数组
 ```
    let arr1 = [1,2,3];
    let arr2 = ['a','b', 'c'];
    array.push.apply(arr1, arr2);
    //[1,2,3,'a','b','c']
    
 ```
首先要确认的是apply第二个参数必须是个数组， 那么就得保证调用apply这个方法的function支持传入数组
其次 这个方法为什么不用concat更简单，是因为concat返回的是一个新的数组，而apply是append，有本质的区别
###使用apply来匹配一个构造函数
```
    Function.prototype.construct = function(args) {
        let _new = Object.create(this.prototype);
        this.apply(_new, args);
        return _new;
    }
```
//Object.create(this.prototype); 这种创建方式比较新，文档又提供了几种传统方式
```
    Function.prototype.construct = function (aArgs) {
        var oNew = {};
        oNew.__proto__ = this.prototype;
        this.apply(oNew, aArgs);
        return oNew;
    };
```
```
    Function.prototype.construct = function(aArgs) {
        var fConstructor = this, fNewConstr = function() { 
            fConstructor.apply(this, aArgs); 
        };
        fNewConstr.prototype = fConstructor.prototype;
        return new fNewConstr();
    };
```

```
    Function.prototype.construct = function (aArgs) {
        var fNewConstr = new Function("");
        fNewConstr.prototype = this.prototype;
        var oNew = new fNewConstr();
        this.apply(oNew, aArgs);
        return oNew;
    };
```
###example
```
    let Person = function () {
        for(let i = 0; i < arguments.length; i ++) {
            this[ 'test' + i] = arguments[i];
        }
    }
    let params = [1, 'yy', '23'];
    let man = Person.construct(params);

    console.log(man.test0) //1
    console.log(man instanceof Person) //true
    console.log(man.constructor) //Person.construct
```
###Function.prototype.bind()
    **定义**
    The bind() method creates a new function that, when called, has its this keyword set to the provided value, with a given sequence of arguments preceding any provided when the new function is called.

    当一个给定参数序列的方法调用bind()的时候， 将'this'这个关键词作为提供的值给这个方法 

    

## 后记

2018, 你好。 2017, 安好

—— yy 后记于 2018.01