第15章  函数
函数是可以被调用的值。-种定义函数的方式叫作函数声明。例如，下面的代码定
义了一个叫id的函数，它具有-一个参数x:
function id(x) {
	return X;
}
return语句使得id会返回一个值。可以通过函数名，同时加上括号中的参数,来调用一个函数: .
	> id( 'hello')
	'hello'
如果一个函数没有任何东西返回，那么默认会返回undefined:
	> function f() { }
	> f()
	undefined
15.1 JavaScript中函数的3中形式
●非方法的函数（“普通函数”）
  可以直接调用函数，会像一个普通函数一样执行
  	id（‘hello’）
  通常，函数的命名以小写字母开头
●构造器
new Function()
通常，函数作为构造器，其命名都以大写字母开始
●方法
你可以将-一个函数存储为-一个对象的属性，这时候它将变成一个方法，而你可
以通过对象来调用它。例如:
	obj .nethod()
方法的名称也是以小写字母开头

15.2 术语：“形参”和“实参”
●形参被用在定义函数时。
	function foo(param1,param1){···}
●形参在函数调用时被调用。
	foo(4,3)

15.3 定义函数
三种创建函数的方式：
●通过函数表达式
●通过函数声明
●通过Function()构造器
所有的函数都是对象、Function构造器的实例：
	function id(x){
		return x;
	}
	console.log(id instanceof Function);//true

15.3.1 函数表达式
函数表达式会产生一个值——函数对象，例如：
var add=function(x,y){returnx+y};
console. log(add(2, 3)); // 5
上面的代码中，我们将一个函数表达式的结果赋值给了变量add,并通过这个变量名来调用
函数。函数表达式的值可以赋给-一个变量(正如上面例子中的)，可以作为传入别的函数的参数等。
由于普通的函数表达式没有名字,它们也被称为匿名函数表达式。.
●具名函数表达式
你可以给函数表达式-一个名字，具名的函数表达式使得函数表达式可以引用它自
己，这对于递归很有用:
var fac = function me(n) {
	if(n>0){
	return n*me(n-1);
} else {
	return 1;
};
console.log(fac(3)); // 6
具名函数表达式的名字只能在函数表达式内部被访问

15.3.2 函数声明
	function add(x,y){
		return x+y;
	}
上面的代码看着像是一个函数表达式，但是它是一一个语句。它与下面的代码基本是等价的:
var add = function (x, y) {
	return x + y;
};
换句话说，函数声明定义了一个新的变量，创建了一个函数对象，并将函数对象赋
值给这个新的变量。

15.3.3 Function构造器
Function（）构造器执行传给它的JavaScript代码字符串。
var add=new Function('x','y','return x+y');

15.5 函数的名称
大多数的JavaScript引擎对于函数对象都会提供--个非标准的name属性。函数的
声明会创建这样的一个属性:
	>function f1() {}
	> f1. name
	'f1'
而匿名函数表达式的name则是一个空字符串:
	> var f2 = function () {};
	> f2. name
具名函数表达式也有一个name:
	> var f3 = function myNane() {};
	> f3.name
	'myName '
函数的名称对于debug是非常有用的。正因如此，有一些开发者往往会给函数表达
式加上名字。




