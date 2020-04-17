第17章  对象和继承
JavaScript中的面向对象编程(OOP)分为如下几层。
●第1层:单一对象的面向对象。
●第2层:对象间的原型链。
●第3层:作为实例工厂的构造函数，类似于其他语言中的类。
●第4层:子类,通过继承已有的构造函数，创建新的构造函数。

17.1 第一层：单一对象
笼统地说，JavaScript 中所有的对象都是从字符串(string) 到值(value) 的映射。
一个对象中的某- -项(键、值)称为属性。属性的键(key) 始终是文本字符串。
属性的值(value) 可以是任何JavaScript值，包括函数。方法是值为函数的属性。
17.1.1 属性的种类
属性可以分为3种。
（1）属性（property,或称为数据属性）
对象中的普通属性(即从字符串的键到值的映射)，包括方法(method)被称为
数据属性。这是到目前为止最常见的属性类型。
(2)访问器(Accessor， 或称为访问器属性)
访问器是类似于读、写属性的特殊方法。属性的值存储在普通属性中，而访问
器可以计算属性的值。你可以把它们看成是虚拟的属性。详见17.7 “访问器
(getter和setter)”。
(3) 内置属性(Internal property)
只存在于ECMAScript语言规范中。它们不能用JavaScript 直接访问，但也许可
以用间接的方式访问。规范将内置属性的键置于方括号中。例如，[Prototype]]
持有一个对象的原型，并可以通过Object.getPrototypeOf)来访问。

17.1.2 对象字面量
JavaScript的对象字面量可以直接创建简单对象(Object 的直接实例)。下面的代
码使用对象字面量，把一个对象赋值给变量jane。这个对象有2个属性: name
和describe。其中，describe是一个方法:
var jane = {
	name: ' Jane' ,
	describe: function () {
		return 'Person named '+this.nane; // (1)
	}，// (2)
};
(1)在方法中使用this来指代当前对象(也被称为方法调用的接受者)。
(2) ECMAScript 5允许在对象字面量的最后一个属性之后跟一个逗号。哎，并不
是所有的旧浏览器都支持。最后的逗号十分有用，因为你可以重新排列属性而不用
担心哪个属性放在最后。
你可能存在这样的印象，对象只是从字符串到值的映射。但远不止这些，它们才是
真正意义.上的通用对象。例如，可以使用对象间的继承保护对象不被修改。直接创建
对象的能力是JavaScript一个显著特点:你可以在没有类的情况下直接创建对象，然后
进行抽象处理。例如，构造函数一创建对象的工厂大致类似于其他语言中的类。

17.1.3 点运算符（.）:通过固定键值访问属性
点操作符提供了一种简洁的语法访问属性。属性的键必须是标识符。如果你想使用任意
的名称来读、写属性，那么需要使用中括号操作符([]): 通过计算出的键访问属性”。
var jane = {
	name: ' Jane',
	describe: function () {
		return 'Person named ' +this . name;
	}
}
●获取属性
点操作符可以"获取”一个属性(读取它的值)。下面是一些例子:
> jane.name // get property‘ nane'
'Jane'
> jane.describe // get property 、 describe'
[Function]
获取一个不存在的属性会返回undefined:
>jane . unknownProperty
undefined
●调用方法
点操作符也可用于调用方法:
> jane.describe() // call nethod . describe '
'Person naned Jane'
●设置属性
你可以使用赋值操作符(=)为点操作符指向的属性赋值，例如:
> jane.nane = ' John'; // set property‘ name'
> jane 。describe()
'Person naned John '
如果一个属性不存在，给它赋值时会自动创建该属性。如果一个属性已经存在，给
它赋值时会改变该属性的值。
●删除属性
delete操作符允许从一个对象中完全移除-一个属性(整个键-值对)。例如: 
> var obj = { hello: 'world' };
> delete obj. hello
true
> obj. hello
undefined
如果你只是把一个属性设置为undefined，这个属性仍然存在，而且这个对象仍
然包含这个键: .
>var obj={foo:'a',bar:'b'};
> obj.foo = undefined;
> object. keys(obj )
[ 'foo', 'bar' ]
如果你删除这个属性，它的键就不存在了:
> delete obj . foo
true
> object . keys(obj)
[ 'bar' ]
删除只影响一个对象的直接(“自有的”,非继承的)属性。这并不涉及它的原型。
●delete的返回值
如果属性是自有属性，且不能被删除，delete会返回false。其他的所有情况都
会返回true。下面是一些例子。
作为准备，我们创建一个可以被删除的属性和一个不能被删除的属性(17.8.3 “通
过描述符获取和定义属性”阐述了object. defineProperty() )。
var obj = {};
object . defineProperty(obj, ' canBeDeleted', {
	value: 123,
	configurable: true
});
object . defineProperty(obj, . cannotBeDeleted', {
	value: 456,
	configurable: false
});
delete不能删除自有属性时，返回false:
> delete obj . cannotBeDeleted
false
其他所有情况下删除属性，返回true:
> delete obj . doesNotExist
true
>delete obj . canBeDeleted
true
delete操作即使没有产生任何改变，也会返回true (继承的属性不会被移除):
> delete obj . toString
true
> obj. toString // still there
[Function: toString]

17.1.5 中括号操作符（[ ]）:通过计算出的键访问属性
●通过中括号操作符获取属性
中括号操作符可以通过表达式计算得出一个属性的键:
> var obj = { someProperty: 'abc' };
> obj['some' + 'Property']
' abc'
> var propKey = ' someProperty';
> obj[propKey]
' abc'
该操作符也可以通过非标识符的键访问属性:
> var obj = { 'not an identifier': 123 };
> obj['not an identifier']
123
请注意，中括号操作符强制括号中的内容转化为字符串。例如:
>varobj={'6':'bar'};
> obj[3+3] // key: the string '6'
'bar'
●通过中括号符调用方法
根据你的期望调用方法:
> var obj = { myMethod: function () { return true } };
> obj[ ' myMethod']()
true
●通过中括号操作符设置属性
设置属性与点操作符类似:
>varobj={};
> obj[ ' anotherProperty'] = 'def';
> obj .anotherProperty
'def'
●通过中括号操作符删除属性
删除属性也与点操作符类似:
> var obj = { 'not an identifier': 1, prop: 2 };
> object. keys(obj)
[ 'not an identifler', ' prop' ]
> delete obj[ 'not an identifier ']
true
> 0bject. keys(obj)
[ 'prop' ]

17.2 把任意值转化为对象
        值                              结果
(无参数调用)                          {}
undefined                             {}
null                                        {}
布尔值bool                        new Boolean(bool)
数字num                           new Number(num)
字符串str                           new String(str)
对象obj                              obj(无改变，不需要转化)
下面是一些例子:
> 0bject(null) instanceof object
true
> 0bject(false) instanceof Boolean
true
>varobj={};
> 0bject(obj) === obj
true
下面的函数检查value是否为对象:
function is0bject(value) {
return value === 0bject(value);
}
注意，如果value不是对象，前面的函数会创建-一个对象。你也可以用typeof实
现相同的功能。
也可以用构造函数的方式调用object,这和以函数的方式调用产生的结果相同。
>var obj={};
> new 0bject(obj) === obj
true
> new 0bject(123) instanceof Number
true

17.3 this作为函数和方法的隐式参数
(1)宽松模式中的普通函数
尽管普通函数中的this没有实际用处，但它仍然作为一个特殊的变量存在，
它的值总是指向全局对象(在浏览器中是window,详见16.8“全局对象”):
> function returnThisSloppy() { return this }
> returnThissloppy() === window
true
(2)严格模式中的普通函数
this总是undefined:
> function returnThisStrict() { 'use strict'; return this.}
>returnThisStrict() === undefined
true
(3)方法.
this指向调用方法的对象:
> var obj = { method: returnThisStrict };
> obj .method() === obj
true
在这个例子的方法中，this的值被称为方法调用的接受者。

17.5 遍历和检测属性
(1)继承(自有属性与继承属性)
一一个对象的自有属性直接存储在该对象中。继承的属性存储在该对象的其中一
个原型中。
(2)枚举(枚举属性与非枚举属性)
属性的枚举性是一一个特性,标识为true或false. 枚举性影响较小，通常可以忽略。
你可以列出自有的属性键(keys)， 也可以列出所有的可枚举的属性键，并检查这
个属性是否存在。

17.5.1 列出自有的属性键
你可以列出所有自有的属性键，也可以只列出可枚举的属性键:
●Object . getOwnPropertyNames (obj)返回obj的所有自有的属性键。
●object.keys(obj) 返回obj的所有可枚举的属性键。
注意，属性通常都是可枚举的(详见17.8.7 "枚举性:最佳实践”)，因此你可以使
用object.keys()， 特别是对已经创建的对象。、
17.5.2 列出所有的属性键;
如果想列出一个对象的所有属性(包括自有的和继承的)，那么你有两个选择。
选择1，使用循环:
for (uvariable in object)
	statement
可以遍历object的所有可枚举的属性键。更全面的描述参考13.3.5 “for-in"。
选择2，自己实现一个函数，遍历所有属性(不仅是可枚举的)。例如:
function getAllPropertyNames(obj) {
	var result = [];
	while (obj) {
		// Add the own property names of‘ obj' to‘ result'
		Array. prototype . push. apply(result, object . getOwnProper tyNames(obj));
		obj = object . getPrototype0f(obj);
	}
	return result;
}

17.5.3. 检测属性是否存在
你可以检查-一个对象是否具有某个属性，或者一个属性是否直接存在于某个对
象中:
propKey in obj
如果obj拥有-一个键为propKey的属性，则返回true。这个测试也包括继承的属性。
0bj ect. prototype . hasOwnProperty (propKey)
如果接受者(this )拥有一个键为propKey的自有(非继承)属性,则返回true。
避免在一个对象上，直接调用hasOwnProperty()， 因为它可能会被重写(例如，被一个键为hasOwnProperty的自有属性重写):
> var obj = { hasOwnProperty: 1, foo: 2 };
> obj.hasOwnProperty('foo') // unsafe .
TypeError: Property ' hasownProperty' is not a function
相反，最好通过泛型调用：
>object. prototype. hasOwnProperty .call(obj, 'foo') /1 safe
true
> {}. hasOwnProperty . call(obj, 'foo') // shorter
true

17.6 遍历自有属性
遍历属性键:
●结合for-in 和hasOwnProperty(),。这种方式甚至可以在以前的JavaScript 引擎上运行。例如:
for (var key in obj) {
	if (0bject . prototype . hasOwnProperty. call(obj, key)) {
		console.' log(key);
	}
}
object. keys()或object. getOwnPropertyNames()与forEach()结合遍历数组:
var obj = { first: 'John', last: 'Doe' };
// Visit non- inheri ted enunerable keys
object . keys(obj) . forEach(function (key) {
console. log(key);
});
遍历属性值或键值(key, value) 对:
●遍历所有的键,然后用每个键获得对应的值。其他语言提供了简单的处理方法，
但JavaScript没有。

17.7 访问器（getter和setter）

17.7.1
下面的例子使用对象字面量为属性foo定义setter和getter:
var obj={
	get foo() {
		return' getter' ;
	},
	set foo(value) {
		console. log( 'setter: ' +value);
	}
};
下面是执行结果:
> obj.foo = 'bla';
setter: bla
>obj.foo
'getter'

17.7.2 通过属性描述符定义访问器
另一种方法是通过属性描述符指定getter 和setter (详见17.8.2“属性描述符”)。下
面的代码与前面字面量定义了相同的对象:
var obj = object . create(
	0bject . prototype, { // object with property descriptors
		foo: { // property descriptor
			get: function () {
				return ' getter';
			}
			set: function (value) {
				console. log( 'setter: ' +value);
			}
		}
	}
);

17.7.3 访问器和继承
getter和setter 继承自原型:
> var proto = { get foo() { return 'hello'} };
> var obj = object. create(proto);
>obj.foo
'hello'

17.8 属性特性和属性描述符
●属性特性是属性的原子构建块。
●属性描述符是- -种数据结构，用于编程处理特性。

17.8.1 属性特性
所有的属性状态，包括它的数据和元数据，都存储在特性中。它们是属性拥有的字
段，就像对象拥有的属性。特性的键通常写在双方括号中。特性与普通属性和访问
器(getter 和setter)有关。
下面是普通属性具有的特性。
●[ [Value]]持有属性的值，即它的数据。
●[[Writable]]持有布尔类型的值，表示属性值是否可以改变。
下面是访问器具有的特性:
●[[Get]] 持有getter, 读取属性时调用的函数。该函数计算读取的结果。
●[[Set]] 持有setter, 为属性设置值时调用的函数。该函数接收设置的值作为参数。
所有的属性都有如下特性:
●[[Enumerable]] 持有一个布尔值。设置-一个属性不可枚举， 那么在某些操作中
会隐藏此属性(详见17.5“遍历和检测属性”)。
●[ [Configurable] ]持有一个布尔值。如果它是false,那么你不能删除、
改变属性的任何特性(除了[ [Value]]),也不能把它从数据属性转换为访问
器属性，反之亦然。换句话说，[[Configurable]] 控制属性元数据的可写
性。由于历史原因，该规则有一个例外，JavaScript 允许把非配置属性从可写
状态变成只读状态。 数组的属性length 总是可写的，且不可配置。
如果没有这个例外，就无法冻结数组。

17.8.2 属性描述符
属性描述符是用于编程处理特性的一种数据结构。它是一-个编码属性特性的对象。
每个描述符的属性对应一个特性。例如，下面是一个只读属性的描述符，该属性的
值是123:
value: 123,
writable: false,
enumerable: true ,
configurable: false
}
使用访问器，可以实现同样目的。描述符看起来如下:
{
get: function () { return 123 },
enumerable: true ,
configurable: false

17.8.3通过描述符获取和定义属性
属性描述符用于两种操作。
(1)获取属性
一个属性的所有特性都通过一一个描述符返回。
(2)定义属性
定义属性意味着会有一些不同，这取决于该属性是否已经存在。
●如果属性不存在，会创建-一个新的属性,它的特性由描述符指定。如果描
述符中没有特性对应的属性,则使用默认值。默认值由特性名的意义指定。
这与通过赋值创建属性(属性可写，可枚举和可配置)时使用的值是相反的。例如:
>varobj={};
> object . defineProperty(obj, 'foo' ，{ configurable: true });
> 0bject .getOwnPropertyDescriptor(obj, 'foo' )
{ value: undefined,
writable: false,
enunerable: false ,
configurable: true }
●如果属性已经存在， 那么更新描述符指定的属性特性。如果描述符中的属
性没有对应的特性，则特性不变。下面有个例子(继续使用前面的例子):
> Object . defineProperty(obj, 'foo', { writable: true });
> object . getOwnProper tyDescriptor(obj, ' foo' )
{ value: undefined,
writable: true,
enumerable: false ,
configurable: true }







