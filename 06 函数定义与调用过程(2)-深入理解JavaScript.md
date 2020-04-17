第15章
 15.7 控制函数调用：call(),apply()和bind()
所有的函数(记住这里说的函数是指对象以及对象的方法)都具有call(),apply()
和bind()方法。它们可以在执行方法时用一个值指向this,并改变面向对象的作
用域。

15.7.1 func.apply(thisValue,argArray)
此方法在调用函数func时，argArray参数将作为函数的arguments传递给函
数，以下的两种表达式是等价的:
	func(arg1, arg2, arg3)
	func. apply(null, [arg1, arg2, arg3])
而thisValue这个参数则可以指定执行func时的this值。在非面向对象的调
用中它不是必要的，因此这里我们将它设定为nu1l。
apply()往往用在一个函数需要以类似数组(但又不是数组)的形式接受多个参数。
多亏有了apply()，我们可以使用Math.max() 来获得一个数组中最大的元素:
	>Math.max(17, 33, 2)
	33
	> Math. max . apply(null, [17, 33, 2])
	33

15.7.2 func.bind(thisValue,arg1,···,argN)
这个方法会执行部分的函数功能，它会创建一个新的函数,这个函数会调用func,
并会将thisValue指定为this，同时应用以下参数: arg1 直到argN,紧随其
后的是新函数的实际参数。在下面的非面向对象的调用中，我们将thisValue设
定为了null。
使用bind()来创建一个新的plus1 ()函数，它类似add()函数,但是由于指定x始终为1,
所以只需要传入参数y:
	function add(x, y) {
		return x + y;
	}
	var plus1 = add . bind(null, 1);
	console . log(plus1(5)); // 6
事实上，我们用类似以下代码创建了-一个新的函数:
	function plus1(y) {
		return add(1, y);
	}

15.8 参数缺失或者超出时的处理
JavaScript中并不限制一个函数的参数数量:因此你事实上可以抛开函数定义的参
数限制，向一个函数传入任意数量的实参。这就导致了实参和形参可能出现两种
差异:
 (1)实参数量多于形参
     对此，多余的参数是会被忽略的，但是能在类数组的arguments中被获取到。
 (2)实参数量少于形参.
     缺失的形参都会被赋予undefined的值。

15.8.1 通过索引访问所有参数：神奇的arguments变量
arguments变量非常特别，并且它只存在于函数中(包括方法)。它是一个类数
组的对象，包含了当前函数调用的所有实参。我们来看看下面的代码:
	function logArgs() {
		for (var i=0; i<arguments.length; i++) {
			console. log(i+'. '+arguments[i]);
		}
	}
下面是调用的结果:
	> logArgs( 'hello'，'world')
	0. hello
	1. world
arguments有以下的特征:
●它是类数组的，但又非数组。一方面，它有一个length属性，所有的参数都
可以通过索引值来进行读写。
另一方面，arguments 不是一个数组，它仅仅是类似。它并没有数组的方法.
(slice(),forEach)等)。谢天谢地，我们可以借用数组的方法或者将arguments转
化为数。
●它是一个对象，因此它支持所有的对象方法和操作。例如，可以使用in操
作符来检测arguments是否包含有指定索引位的参数。
	> function f() { return 1 in arguments }
	> f('a')
	false
	> f('a', 'b')
	true
你也可以用类似的方式使用hasOwnProperty() 
	> function g() { return argunents. hasOwnProperty(1) }
	>g('a', 'b' )
	true

15.8.3 可选参数
如果参数是可选的，这意味着当你没有指定这个参数，它将会使用默认值。类似于
强制性参数，我们有四种处理可选参数的选择。
第一种，检测undef ined:
function bar(arg1, arg2, optional) {
	if (optional === undefined) {
		optional = 'default value';
	}
}
第二种，将可选参数转换为布尔值:
f (!optional) {
	optional = 'default value';
}
第三种，可以使用或运算符，如果左侧操作数不是false值，则返回左侧的操作数，
否则将返回右侧的操作数:
// 0r operator: use left operand if it isn't falsy
optional = optional || 'default value';
第四种，你可以使用arguments . length来检测函数支持的最小数量的参数。
if (arguments.length < 3) {
	optional = 'default value';
}
同样的，最后一种方式有别于其他的方式: 
●前三种方式无法区分bar(1,2)和bar (1, 2, undefined)。这两种情况下，
optional的值都是默认值。
●第四种方式对于bar(1,2) 会使用默认值，而对于bar (1,2, undefined)
会保留undefined的传人。
还有一种处理可选参数的方式是使用具名参数,通过对象字面量的属性的方式。

15.8.4 模拟参数的引用传递
在JavaScript中，不能传递参数的引用:因此，当将一个变量传递给一个函数,它的值
会被复制一份并传递给函数(值传递)。因此，在函数中，无法改变变量。
如果想要这么做，必须将变量的值包装到一个数组中。
下面的例子展示了在函数中如何递增一个变量:
function incRef(numberRef) {
	numberRef[0]++;	
}
var n = [7];
incRef(n);
console. log(n[0]); // 8

15.9 具名参数
在编程语言中调用函数或者方式时，你必须将实参(由调用者指定)和形参(函数
定义中指明)做映射。通常有两种方式:
●通过位置来映射的位置型参数。第-一个实参对应第-一个形参，第二个实参对应
第二个形参，以此类推。
●具名参数则通过名称(标识)来做变量的映射。在--个函数的定义中名称会与.
形参做关联，在函数调用中，名称则与实参做关联。只要参数的名称正确，你不需
要关心参数名的顺序。
具名参数有两个优点:它们对函数的参数进行了描述，同时它们对于处理可选参数
的场景也很有用。我先来解释--下这些优势，然后我们来看看如何通过对象字面量
模拟具名参数。

15.9.3 在JavaScript中模拟具名参数
通过对象字面量来命名参数，并将对象作为一个实参传入函数
	selectEntries({ start: 3, end: 20, step: 2 });
函数接受了一一个包含start, end,step属性的对象，而你可以省略其中任意一
个属性:
	selectEntries({ step: 2 });
	selectEntries({ end: 20, start: 3 });
	se LectEntries();
在selectEntries ()中你可以这样实现:
	function selectEntries(options) {
		options = options | | {};
		var start = options.start | | 0;
		var end = options.end | | getDbLength();
		var step = options.step | | 1;
}
你也可以将位置型参数和具名参数相结合。就像是下面的这种形式:
selectEntries(posArg1, posArg2, { nanedArg1: 7, nanedArg2: true });

第16章  作用域、环境和闭包
16.1 定义变量
在JavaScript中，在使用变量前，你可以通过var语句来定义变量:
	var foo;
	foo = 3; // OK, has been dec lared
	bar = 5; // not 0K, an undeclared variable
你也可以将赋值语句与变量定义语句合并进行变量的初始化:
	var foo = 3;
一个未初始化的变量的值是undefined: 
	>var x;
	>X
	undefined

16.2 背景知识：静态性和动态性
(1)静态性(或词法性)
你可以无须执行程序而只从程序源码的角度来看程序的工作。我们看下面的代
码，我们可以肯定函数g被函数f包围在内部:
	function f() {
		function g() {
		}
	}
描述性的词法就像是静态声明，因为它们都涉及了程序的词法
(2)动态性
它表示一一个程序在运行期所表现出来的特性。我们看看下面的代码:
	function g() {
	}
	function f() {
		g();
	}
当我们调用f(),它会调用g()。在执行期间，g被f调用代表了一种动态的
关系。

16.3 背景知识：变量的作用域
(1)变量的作用域
变量的作用域是指变量在何处可以被访问到。例如:
	function foo() {
		var X;
	}
这里，x的直接作用域是函数foo()。
(2)词法作用域
JavaScript中的变量都是有词法作用域的，因此-一个程序 的静态结构决定了-一个
变量的作用域(这个作用域并不会被函数从哪里调用等影响)。
(3)嵌套作用域
如果在一个变量的直接作用域中嵌套有多个作用域，那么这个变量在所有的这
些作用域中都可以被访问:
	function foo(arg) {
		function bar() {
			console.log('arg: ' +arg);
		}
		bar();
	}
	console . log(foo( 'hello')); // arg: hello
arg的直接作用域是foo() ,但是它同样可以在嵌套的作用域bar ()中被访问。
我们说嵌套，foo ()是外部的作用域，而bar ()是内部作用域。
(4)覆盖
如果在一个作用域中声明了一个与外层作用域同名的变量，那么在这个内部作
用域以及其内部的所有作用域中将会访问不到外面的变量。而内部的变量的变
化并不影响外部的变量，当离开内部作用域，外部变量又可以被访问了。
	var x = "global";
	function f() {
		var x = "local";
		console.log(x); // local .
	}
	f();
	console.log(x); // global
在函数f()中，全局的x被本地x所覆盖。

16.4 变量以函数为作用域
JavaScript 的变量是函数级作用域的:只有函数可以产生新的作用域;
代码块在作用域中是不起作用的。例如:
	function main() {
		{ // block starts
		var foo = 4;
		} // block ends
		console. log(foo); // 4
	}
换句话说，foo 在main ()中都是可以被访问到的，而不仅仅是在块中。

16.5 变量声明的提前
JavaScript会提前所有的变量声明，它会把所有的声明移到直接作用域的最前面。
很明显当我们试图在变量声明前来访问它会发生什么:
	function f() {
		console.log(bar); // undefined
		varbar='abc';
		console. log(bar); // abc .
	}
我们可以看到变量bar在函数f()的第-行已经存在了，只是还没有值;因此，被
提前的是变量的声明，而非变量的复制。实际上，JavaScript 中是这样执行f()的:
	function f() {
		var bar;
		console. log(bar); // undefined
		bar = 'abc';
		console. log(bar); // abc
	}
如果你定义一一个已经被定义过的变量，那么什么也不会发生( 包括变量的值，也不
会变化):
	>var x=123;
	> var x;
	>x
	123
函数的声明也会被提前，但是与变量截然不同，函数声明会被整体提前，而不仅仅
是变量的创建的那部分。

16.6 通过IIFE引入新的作用域
如果你想为代码的“执行”块引入一个新的作用域，你可以定义一个函数并立即执
行它。这是一种对块作用域的模拟方式:
function f() {
	if (condition) {
		(function () { // open block
			var tmp = ...;
		}()); // close block
	}
}
这是JavaScript中的一种常见的模式。Ben Alman将它命名为立即执行的函数表达式
(ttp://bit.ly/i-ife) (IIFE， 发音为"ffy”)。 通常来说，- 一个IIFE看起来是这样的:
	(function () { // open IIFE
		// inside IIFE
	}()); // close IIFE
关于IIFE,我们有--些要注意的:.
(1)它是立即执行的
函数的结束大括号后面的括号是用来立即调用函数的。函数体会立即执行。
(2)它必须是一个表达式
如果一个语句以function关键词开头，解析器会认为它是-一个函数定义。但是一个函数
声明并不会立即执行。因此，我们在语句前加了一个左括号告诉解析器function关键词
是处于一个函数表达式的开头。在括号中，只存在表达式。
(3)别忘了后面的分号
如果在两个IIFE之间遗漏了分号，你的代码可能会出现问题:
	(function () {
	}()) // no semicolon
	(function () {
	}());
上面的代码会被翻译为一个函数调用一前一个 IFE (包含它的括号)是被调
用的函数，而后一个IIFE则作为变量。

16.6.1 IIFE变体：前缀运算符
你可以通过前缀运算符来强制执行一个表达式。例如，你可以用逻辑非:
	!function () { // open IIFE
		// inside IIFE
	}(); // close IIFE
或者是void运算符
	void function () { // open IIFE
		// inside IIFE
	}(); // close IIFE
使用前缀运算符的好处是忘了分号结尾并不会造成任何问题。
IIFED的其他使用场景
●避免全局变量，隐藏全局作用域的变量。
●创建新的环境，避免共享。
●保持全局的数据对于构造器的数据相对独立。
●将全局数据附加到单例对象上。
●将全局数据附加到方法中。

16.7 全局变量
包含整个程序的作用域叫作全局作用域或是程序作用域。这就是进入你编写的脚本
时(Web页面上面的<script>或是.js文件)所在的作用域。在全局作用域中，你
可以定义函数来创建嵌套的作用域。每-一个作用域都可以访问它内部以及其父作用
域中的变量。由于全局作用域包含了所有的作用域，它里面的变量便可以在任何地
方被访问到:
	// here we are in global scope
	var globalVariable = 'xyz';
	function f() {
		var localVariable = true;
		function g() {
			var anotherLocalVariable = 123;
			// All variables of surround scopes are accessible
			localVariable = false ;
			globalVariable = ' abc' ;
		}
	}
	// here we are again in global scope

16.8 全局对象
ECMAScript规范中定义使用内部数据结构环境来存储变量(可见16.9“环境:变
量的管理”)。JavaScript 有-一些不常用的特性可以通过一个对 象来为全局的变量创
建环境，这个对象就叫作全局对象。全局对象可以用来创建、读取或是修改全局变
量。在全局作用域中，this 指向全局对象:
	> var foo = 'hello';
	> this.foo // read global variable
	'hello'
	> this.bar = 'world'; /1 create global variable
	> bar
	'world'
要注意的是，全局对象有原型。如果你要罗列它所有(自己以及继承)的属性，你
可以使用类似getAl1PropertyNames ()的函数:
	> getAllPropertyNames(window) . sort().slice(0, 5)
	[ 'AnalyserNode', 'Array', 'ArrayBuffer', 'Attr', 'Audio' ]
JavaScript之父Brendan Eich认为全局对象的设计是他“最大的遗憾”。它影响了性
能，使得变量的作用域的实现变得更为复杂，并且导致模块化代码的减少。

16.9 环境：变量的管理
当程序运行到变量所在的作用域时，变量被创建。这时它们需要-一个存储的空间。
而JavaScript中提供存储空间的数据结构就被称为环境。它将变量名与变量的值做
映射。其结构与JavaScript对象非常类似。有时候在你离开作用域时，环境还依然
会存在。因此，它们是被存储于堆中，而非栈。
变量有两种传递的方式，如果有必要，有两种维度。
(1)动态维度:调用函数
每当一个函数被调用，它就需要给它的参数和变量准备新的存储空间。当调用
结束之后，空间通常会被释放。我们来看看下面的示例函数的实现。它进行了
多次的自递归调用，并且每一-次，它都为n创建新的存储空间:
	function fac(n) {
		if(n<=1){
			return 1;
		}
		return n * fac(n - 1);
	}
(2)词法维度:与外部作用域进行关联
无论-一个函数被调用了多少次，它总要访问它自己(最新)的本地变量和外部
作用域的变量。例如下面的doNTimes函数，它内部有-一个辅助函数
doNT imesRec,当doNTimesRec多次调用自己的时候，每- -次调用都会创建
一个新的环境。然而，doNTimesRec在这多次调用中却一直与doNTimes的
环境保持着联系(类似于所有的函数都共享了同一个全局环境)。在第- -行中,
doNTimesRec就需要通过这个联系来访问action。
	function doNTimes(n, action) {
		function doNTinesRec(x) {
			if(x>=1){
				action(); // (1)
				doNTimesRec(x-1);
			}
		}
		doNTimesRec(n);
	}
总结：
(1)动态维度:执行上下文的栈
函数每调用一次，就会创建-一个新的环境将(变量和参数的)标识符和变量做
映射。对于递归的情况，执行上下文，即环境的引用是在栈中进行管理的。这
里的栈对应了调用栈。
(2)词法维度:环境链.
为了支持这一-维度，JavaScript 会通过内部属性[ [Scope] ]来记录函数的作用
域。在函数调用时，JavaScript 会为这个函数所在的新作用域创建一一个环境。 这
个环境有一个外层域(outer),它通过[[Scope] ]创建并指向了外部作用域的
环境。因此，在JavaScript中-直存在一一个环境链，它以当前环境为起点，
连接了- -层外部的环境。每-一个环境链最终会在全局环境(它是所有函数初始化调用
的作用域）终结。而全局环境的外部环境指向了null。
为了识别环境中的标识，整个环境会从当前环境开始遍历。














