## JS原型链怎么理解?

```objc
var animal = function(){};
var dog = function(){};

animal.price = 2000;
dog.prototype = animal;
var tidy = new dog();

console.log(dog.price) 
console.log(tidy.price) 
```

```objc
解读:
console.log(dog.price) //为什么输出 undefined 
console.log(tidy.price) //为什么输出 2000

因为原型链是依赖于__proto__，而不是prototype。
dog是函数对象，本身没有price属性，此时dog的__proto__属性指向的是其构造函数的原型。
dog的构造函数就是Function，因为var dog = function(){};
语句实际上是是var dog = new Function();
所以，dog.__proto__ == Function.prototype；
而Function.prototype并没有price属性，如果加一句：Function.prototype.price = 123；那么第一个打印就是123；

如此依赖，tidy是一个普通对象，由dog函数构造而来，因此tidy__proto__ == dog.prototype == animal；

所以当tidy上找不到price属性时，会从__proto__寻找原型上的方法，找到animal对象，animal对象有price属性，则返回。
```
