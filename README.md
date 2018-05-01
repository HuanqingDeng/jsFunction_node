## 函数
### 函数对象
首先来理解一下什么事对象：ECMA-262是这样定义的“无序属性的集合，其属性可以包含基本值、对象或者函数”，也就是说，对象是‘名/值’对的集合。

```
var person=new Object();
person.name="Jake";
person.age=18;
person.sayName=function(){
    alert(this.name);
}
```
上面的例子创建了一个名为person的对象，并为它添加了两个属性：name、age和一个方法sayName。其中sayName用于显示this.name（将被解析为person.name）的值。

### 函数字面量
我们来看一段代码：

```
var add=function(a,b){
    return a+b;
}
```
函数字面量包含4个部分，分别是：

 - 保留字 function
 - 函数名 add
 - 参数 a、b
 - 语句 return a+b;
函数字面量可以出现在任何允许出现的地方，也可以定义在其他函数中。一个内部函数除了可以访问自己的参数和变量，同时它也能自由地访问它的父函数的变量和参数。通过函数字面量创建的函数对象包含一个连接到外部上下文的链接。这被称为**闭包**

### 闭包
闭包这个概念是js这门语言中一个非常重要而且是很难掌握的知识点！
这里只能浅显地解释一下什么是闭包
我们用一些代码来解释这个定义：

```
function foo(){
    var a=2;
    function bar(){
        console.log(a);
    }
    return bar;
}
var baz=foo();
baz();//2 <--这就是闭包的效果
```
函数bar()的词法作用域能够访问foo()内部作用域，然后将bar()函数本身当作一个值类型进行传递。
在foo执行后，其返回值 bar（）赋值给变量baz并调用baz(),实际上只是通过不同的标识符引用调用了内部的函数bar()。
在foo执行后，foo内部作用域不会被销毁，因为一直都是bar()本身在使用，所哟foo内部作用域依然存在且能够一直存活，以供bar随时进行引用。
bar依然对该作用域保持引用，而这个引用就叫作闭包。

### 函数作用域
作用域的作用：控制着变量与参数的可见性以及生命周期。
定义在函数内部的参数和变量在函数外部是不可见的，而在一个函数内部任何位置定义的变量，在函数内部任何位置都可见。

```
var f00=function(){
    var a=3,b=5;
    var bar=function(){
        var b=7,c=11;     //此时a=3,b=7,c=11;
        a+=b+c;           //此时a=21,b=7,c=11;
    };
                          //此a=3,b=5,c没有定义；
    bar();                //此时a=21,b=5;
}

```

### 调用
调用一个函数会暂停当前函数的执行，传递控制权和参数给新函数。js提供了四种调用模式：方法调用模式、函数调用模式、构造器调用模式以及apply调用模式。


 1. 方法调用模式
当一个函数被保存为对象的一个属性是，我们称之为方法。当一个方法被调用时，this被绑定到该对象，当对象与方法用“.”来连接时，那么它就被当作一个方法来调用了。
```
var person={
    name："Jake",
    age:18,
    sayAge:function(age){
        this.age=age;
    }
};
person.sayAge(19);
docunment.writeln(person.age);//19
```
 2.函数调用模式


当一个函数并不是一个对象的属性时，那么它就是被当作一个函数来调用的

```
var myobject={
}
//添加一个新的方法
myObject.do=function(value){
   this.value=value;
   var that=this;
   var helper=function(){
       that.value=that.value+that.value;
   };
   helper();
}
myObject.do(3);
console.log(myObject.value)//6
```
这里面遇到一个问题，就是在helper函数内部的this指向的时这个函数本省，而不是全局对象。我们找到一个很好的解决方法，那就是**将this赋值给一个全新的变量that**,那么内部函数就可以访问到this，这样就避免了内部函数里面的this错误的绑定。


 3 构造器调用模式

一个函数，如果创建的目的就是希望结合new前缀来调用，那就被称为**构造函数**
如果一个函数前面带上new来调用，那么就会创建一个连接到该函数的prototype（原型）成员的新对象，同时函数中的this会绑定到这个新对象中。

```
var Myobject=function(string){
    this.status=string;
}
Myobject.prototype.get_status=function(){
    return this.status;
}
//创建实例
var newObj=new Myobject("Hello !");
console.log(newObj.get_status());
//Hello !
```
这里要特别注意：按照惯例，构造函数始终都应该以一个**大写字母开头**，而非构造函数则应该以一个小写字母开头。

 4.Apply调用模式

Apply方法让我们构建一个参数组传递给调用函数。它允许我们选择this的值。Apply方法接受两个参数，第一个要绑定给this的值，第二个就是参数数组。

```
var array=[3,4];
var sum=add.apply(null,array);//sum值为 7
```

```
var statusObj={
    status:'Hello'
};
var status=Quo.prototype.get_status.apply(statusObj);
//status值为‘Hello’
```

```
function SpeciallArray(){
    var values=new Array();
    values.push.apply(values,arguments);
    values.toPipedString=function(){
        return this.join("|");
    }
    return values;
}
var color=new SpeciallArray("blue","yellow","red");
console.log(colo.toPipedString);
//"blue|yellow|red"
```