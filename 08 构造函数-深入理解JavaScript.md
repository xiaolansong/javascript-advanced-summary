17.4 第二层 ：对象间的原型关系
两个对象间的原型关系类似继承:每个对象都可以把另一个对象作为它的原型，并
继承原型的所有属性。对象通过内部属性[ [Prototype] ]指定它的原型。每个对
象都有这个属性，而它也可以是null。通过[[Prototype]]属性连接成的对象
链称为原型链。
想要了解基于原型的(或原型的)继承如何工作，我们可以看这个例子(用新引入
的符号来指定[ [Prototype] ]属性):
var proto = {
	describe: function () {
	r	eturn' name: ' +this . nane;
	}
};
var obj={
	[[Prototype]]: proto,
	nane: 'obj '
};
对象obj从proto继承了describe属性。它还有个称为“自有的”(非继承的、
直接的)属性name。

17.4.4 获取和设置原型
●使用给定prototype创建新对象
调用方法:
object. create(proto, propDesc0bj?)
创建原型为proto 的对象。可以选择通过描述符添加属性(详见17.8.2“属性描
述符”)。下面的例子中，对象jane获得原型PersonProto 和值为'Jane '的可
变属性name (通过属性描述符声明):
var PersonProto = {
	describe: function () {
		return ' Person naned ' +this . name ;
	}
};
var jane = object . create(PersonProto, {
	name: { value:' Jane', writable: true }
});
下面是运行结果:
>jane . describe()
'Person naned Jane'
但你通常只需先创建一个空对象，然后手动添加属性，因为描述符比较繁琐。
var jane = object . create(PersonProto);
jane.value = 'Jane';
●读取对象原型
调用方法:
object . getPrototype0f(obj)
返回obj的原型。继续前面的例子:
> object . getPrototype0f(jane) === PersonProto
true
●检查-个对象是否是另一个对象的原型
语法是:
object. prototype. isPrototype0f( obj)
检查方法的接收者是否是obj的(直接或间接)原型。换句话说，接收者和obj
是否在同一原型链上，且obj是否在接受者之前?例如:
>varA={};
> var B = object. create(A);
> var C = object .create(B);
> A.isPrototype0f(C)
true
> C.isPrototype0f(A)
false
●找到定义属性的对象
下面的函数遍历对象obj的原型链。该函数返回键为propKey的自有属性的第
一个对象，如果没有则返回null:
function getDefining0bject(obj, propKey) {
	obj = object(obj); // make sure it's an object
	while (obj && !{} .hasOwnProperty .call(obj, propKey)) {
		obj = object .getPrototype0f(obj);
		// obj is null if we have reached the end
	}
	return obj;
}

17.4.5 特殊属性__proto__
某些JavaScript引擎有特殊属性可以获取和设置对象的原型:__ proto__ 。 这样
可以直接访问[ [Prototype] ]
>var obj={};
> obj.__ proto_ === object. prototype
true
> obj._ proto_ = Array. prototype
> object . getPrototype0f(obj) === Array . prototype
true
关于__ proto__ ， 你需要知道以下几点。
●__ proto__ 读作 “dunder proto”，“double underscore proto” 的缩写。
●__ proto__ 不属于ECMAScript5标准。因此，如果你希望代码遵循这个标准,
且在当前的JavaScript引擎中稳定运行，那么就不能使用__ proto__ 。
●然而，越来越多的引擎开始支持__ proto__ ， 而它也将成为ECMAScript 6的一部分
●下面的表达式检测引擎是否支持特殊属性__ proto__
object . getPrototype0f({_ proto__ : null }) === null

17.4.6 设置和删除仅影响自有属性
只有获取属性才需要考虑对象的完整原型链。设置和删除会忽略继承的属性，仅影
响自有属性。
●设置属性
设置属性会创建一一个自有属性，即使已存在继承了该key的属性。例如，下 面的
代码:
var proto = { foo: 'a' };
var obj = 0bject. create(proto);
obj从proto继承了foo:
>obj. foo
'a'
> obj .hasOwnProperty('foo' )
false
设置foo得到期望的结果:
> obj.foo = 'b';
> obj. foo
'b'
然而，我们已经创建了一个自有属性，且不会改变proto. foo:
> obj .hasOwnProperty( ' foo')
true
>proto. foo
'a'
理由是，原型属性会由多个对象共享。这种方式不会让我们破坏、“改变”原有属.
性，而只是影响当前对象。.
●删除继承的属性
你只能删除自有属性。下 面我们用原型proto再创建-一个对象obj:
var proto = { foo: 'a' };
var obj = object. create(proto);
删除obj继承的属性foo，不起作用:
> delete obj . foo
true
> obj. foo
'a'
● 在原型链的任何位置改变属性
如果你希望改变继承的属性，首先要找到拥有这个属性的对象，然后改变这个对象的
相应属性。例如，我们继续前面的例子，删除属性foo:
> delete getDefining0bject(obj, 'foo'). foo;
true
> obj. foo
undefined

17.10  第3层：构造函数——实例工厂
 构造函数(简称构造器)帮助生成对象。构造函数在某些方面和普通函数类似，但
命名、设置和调用不同。
本节阐述了构造函数的工作原理。它相当于其他语言中的类。
我们已经看过两个相似对象的例子(详见17.4.3“通过原型在对象间共享数据”):
var PersonProto = {
	describe: function () {
	return ' Person named' +this . nane;
};
var jane = {
	[[Prototype]]: PersonProto,
	name: 'Jane'
};
var tarzan = {
	[[Prototype]]: PersonProto,
	nane: ' Tar zan '
};
对象jane和tarzan 都是“人”，并共享原型对象PersonProto。 我们把原
型改为可以创建jane和tarzan对象的构造函数Person。构造函数创建的对
象称为它的实例。这种实例与jane 和tarzan具有相同的结构,包括两个部分:
(1)数据是由实例指定的，并存储在实例对象的自有属性中
(2)行为被所有的实例所共享，它们公用一个带有方法的原型对象
构造函数是通过new操作符调用的函数。按照约定，构造函数的名字以大写字母
开头，而普通函数的名字和方法以小写字母开头。第1部分，设置构造函数自身:
function Per son(nane) {
	this.name = nane ;
}
Person. prototype中的对象成为Person所有实例的原型。这构成了第2部分:
Person. prototype. describe = function () {
	return ' Person naned ' +this . nane ;
};
创建并使用Person的实例:
> var jane = new Person( 'Jane');
> jane. describe()
'Person naned Jane'
我们可以把Person看作普通函数。它只有通过new调用时，才变成构造函数。
new操作符执行步骤如下。
●首先设置行为:创建一个新对象，其原型为Person. prototype。
●然后设置数据: Person接受对象作为隐式参数this,并添加实例属性。
instanceof操作符可以用来检查-一个对象是否为给定构造函数的实例:
> jane instanceof Person
true
> jane instanceof Date
false

17.10.1 JavaScript中new操作符的实现
function newOperator(Constr, args) {
	var thisValue = object . create(Constr . prototype); // (1)
	var result = Constr . apply( thisValue, args);
	if (typeof result === 'object' && result !== null) {
		return result; // (2)
	}
	return thisValue;
}
在行(1)中，可以看到构造函数Constr 创建的实例的原型是Constr.
prototype。
行(2)中,揭示了new操作符的另一个特性:可以从构造函数返回一个任意的对象,
它成为new操作符的返回结果。

17.10.2 术语：两个原型
(1)原型1:原型关系
一个对象可以是另一个对象的原型:
> var proto = {};
> var obj = object. create(proto);
> object . getPrototype0f(obj) === proto
true
前面的例子中，proto 是obj 的原型。
(2)原型2: prototype 属性的值
每个构造函数C都有一一个prototype属性，它指向一一个对象。该对象成为
构造函数C的所有实例的原型:
> function C() {}
> object . getPrototype0f(new C()) === C. prototype
true

17.10.3 实例的constructor属性
默认每个函数C包含一个实例原型对象C.prototype, 它的constructor
属性指回C:
> function C() {}
> C. prototype . constructor === C
true
因为每个实例都从原型中继承了constructor 属性，所以你可以使用它得到实
例的构造函数。
>var0=new C();
>o.constructor
[Function: C]
●constructor属性的用例
(1)切换对象的构造函数
下面的catch子句中，我们根据捕获异常的构造函数的不同，采取不同的
处理:
try {
} catch (e) {
	switch (e. constructor) {
		case SyntaxError :
			break;
		case CustomError :
			break;
		...
	}
}
这个方法只检测给定构造函数的直接实例。相比之下，instanceof 既
检测直接实例，也检测所有子构造函数的实例。
(2)确定对象的构造函数名
例如:
> function Foo() {} 
> var f = new Foo();
> f .constructor . nane
'Foo'
(3)创建相似对象
下面是如何创建一个新的对象y,它和已有的对象x都有相同的构造函数:
function Constr() {}
var x = new Constr();
var y = new x. constructor();
console. log(y instanceof Constr); // true
这个技巧可用于子构造函数( subconstructors)实例的方法，且想要创建-一个和
this相似的新实例。这样你就不能使用一个固定的构造函数:
SuperConstr . prototype . createCopy = function () {
return new this . constructor(...);
};
(4)指向父构造函数( superconstructor )
一些继 承库把父构造函数( superprototype)赋值给子构造函数(subconstructor)
的一个属性。例如，YUI 框架通过Y.extend 提供子类:
function Super() {
function Sub() {
Sub. superclass . constructor . call(this); // (1)
Y.extend(Sub, Super);
行(1)执行的调用，是因为extend 把Sub. superclass设置为Super.prototype。正是由于有了
constructor属性，才可以把父构造函数(superconstructor)作为方法来调用。

确保对每个构造函数C，下面的断言都成立:
C. prototype .constructor === C
默认情况下，每个函数f都有一个设置正确的prototype属性:
> function f() {}
> f. prototype . constructor === f
true
因此你应该避免替换这个对象，而只为它增加属性:
// Avoid:
C. prototype = {
method1: function (...) { ... },
};
// Prefer:
C. prototype .method1 = function (...) { ... };
如果替换了prototype 指向的对象，你应该手动把正确的值赋值给constructor:
C. prototype = {
constructor: C,
nethod1: function (...) { ... },
};
注意，JavaScript中并没有什么重要的东西依赖于constructor属性，但设置
该属性是一种良好的风格习惯，因为这样本节涉及的技术才得以实现。.

17.10.4 instanceof运算符
instanceof运算符:
value instanceof Constr
判断value是由构造函数Constr还是子构造函数( subconstructor)创建的。
它是检测Constr .prototype是否在value的原型链上。因此，下 面的两个表
达式是等价的:
value instanceof Constr
Constr . prototype . isPrototype0f(value)
一些例子：
> {} . instanceof 0bject
true
> [] instanceof Array /1 constructor of []
true
> [] instanceof object /1 super-constructor of [ ]
true
>new Date() instanceof Date
true
> new Date() instanceof 0bject
true
正如所料，instanceof 对基本类型的值总是false:
> 'abc' instanceof Object
false
> 123 instanceof Number
false
最后，如果instanceof的右边不是函数，它会抛出异常:
> [] instanceof 123
TypeError: Expecting a function in instanceof check















