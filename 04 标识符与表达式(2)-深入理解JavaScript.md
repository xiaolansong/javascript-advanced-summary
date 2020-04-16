第九章  运算符

9.1 运算符和对象
所有的运算符都会强制要求它们的运算数为合适的类型。
大部分运算符只对原始值有效( 比如，算术运算符和比较运算符)。
这意味着对象在运算之前会被转换为原始值。
比如加号这个可怜的运算符，许多语言都用它来拼接数组。
但在JavaScript中却不是这样，它会先将数组转换成字符串，然后再把它们拼接起来。
[1,2]+[3]//  '1,23'
String([1,2])//  '1,2'
String([3])//  '3'

9.2 赋值运算符
普通的赋值运算有多种使用方式：
x=value  给前面已声明的变量x赋值
var x=value  在声明变量的时候直接赋值
obj.propKey=value  设置属性
obj['propKey']=value  设置属性
arr[index]=value  设置数组元素
赋值运算是一种表达式，它用来计算要被赋予的值。因此可以进行链式赋值。比如
以下这条语句会同时将x和y赋值为0:
x=y=0;

9.3 等号运算符：===和==
JavaScript有两种方式来判断两个值是否相等。
●严格相等(===) 和严格不等(! ==)要求比较的值必须是相同的类型。
●普通(或者“宽松”)相等(==) 和不等(!=) 会先尝试将两个不同类型的值
   进行转换，再使用严格相等进行比较。
宽松相等可能会存在两个问题。第一，执行转换的过程令人困惑:第二，由于运算
符的宽容特性，使得类型错误可能长时间不能被发现。

9.3.1 严格相等（===，！==）
不同类型的值总是严格不等的。如果两个值的类型相同，则会进行如下的情况。
●是否为undefined === undefined 的比较。
●是否为null === null 的比较。
●比较两个数字。
   x === x// unless x is NaN
   +0 ===-0
   NaN !== NaN // read explanation that fol lows
●比较两个布尔值，两个字符串:结果显而易见。
●比较两个对象(包括数组和函数):当且仅当x和y是同一个对象时，x=== y;
如果你想比较两个不同的对象，则需要自己实现一个比较算法。
   >var b={},c={};
   >b===c
   false
   >b===b
   true
●其他对比：不严格相等
陷阱: NaN 
特殊数字NaN 和本身不相等。
   >NaN===.NaN.
   false

●严格不等(!==)
  严格不等比较:
  x!==y
  等价于对严格相等比较的结果取反:
  !(x === y)

9.3.2 普通（宽松）相等（==，！=）
普通相等的比较算法原理如下。如果两个运算数的类型相同(六种规范类型:
Undefined,Null,Boolean,Number,String和Object其中之一)， 则使用严格相等比较
它们。
否则，如果运算数是如下类型。
1.undefined和null,则它们被认为是宽松相等的。
  > undefined == null
  true
2.一个字符串和-一个数字，则将字符串转换为-一个数字，使用严格相等比较两个运算数。
3.一个布尔值和一一个非布尔值，则将布尔值转换为-一个数字，然后(再次)进行宽松比较。
4.一个对象和一个数字或者一个字符串，则尝试转换此对象为一个原始值然后(再次)进行宽松比较。
5.其他情况——不符合上述提到的情况，宽松比较的结果是false
●宽松不等(!=)
  不等比较操作:
  x !=y
  等价于对相等比较的结果取反:
  !(X == y)
●陷阱:宽松相等与布尔转换不同
  相等和转换为布尔值(参见10.1 “转换成布尔值")的工作原理是不同的。若数字
  大于1,则转换为布尔值true (如if语句)。但这些数字不是宽松地等于true。
  以下的注释演示了这个结果是怎么出来的:
  >2==true//2===1
  false
  >2==false//2===0
  false
  >1==true//1===1
  true
  >0==false//0===0
  类似的，虽然空字符串等于false,但不是所有的非空字符串都等于true:
  >'' == false//0===0
  true
  >'1'=true//1===1
  true
  >'2'==true//2===1
  false
  > 'abc' == true // NaN === 1
  false
●陷阱:宽松相等中的字符串
  有些宽松相等会很有用，不过这取决于你想要什么效果:
  > 'abc' == new String('abc') // 'abc' == ' abc'
  true
  > '123' == 123 // 123 === 123
  true
  但也存在一些其 他的问题，取决于JavaScript 如何将字符串转换为数字
  >'\n\t123\r ' == 123// usually not OK
  true
  >''== 0//0===0
  true
●陷阱:宽松相等中的对象
  如果比较对象和非对象，它们会被转换为原始值，这将导致一些奇怪的结 果:
  > {} =='[object 0bject]'
  true
  > ['123'] == 123
  true
  >[] ==0
  true
  然而，只有当两个对象是同一个对象时才会相等。这意味着无法真正地比较两个包
  装对象:
  > new Boolean(true) === new Boolean(true )
  false
  > new
  Number(123) === new Number(123)
  false
  > new String('abc') == new String( 'abc' )
  false
9.3.3 没有针对==的有效用例
●用例：检测undefined或null
  以下比较语句可以用于保证x既不是undefined也不是null:
  if (x != null)
  如果想要更明确地区分，可以对两种值分别做检测:
  if (x !== undefined && x !== null)
●用例：字符串中的数字
  如果不确定x是数字还是内容为数字的字符串，可以用如下方式来检测:
  if (x == 123) 
  以上语句可以判断出x是123或是'123'。但是，以下方式也很简洁，并且更加明确:
  if (Number(x) === 123)
●用例：比较包装实例和原始值
  原始值和包装的原始值是宽松相等的:.
  > 'abc' == new String( 'abc')
  true
  使用这种方式有三个不好的地方。第一，宽松相等对两个实例对象是无效的:
  > new String('abc') == new String(' abc' )
  false .
  第二，应该避免包装。第三，如果一定要使用这个方法，最好做到更加明确:
  if (wrapped . value0f() === 'abc') 

9.5 加号运算符（+）
简单地说，加号运算符会对它的运算数进行检测。如果其中一个是字符串，另一个
也会被转换为字符串，并对它们进行拼接:
	> 'foo'+ 3
	'foo3 '
	> 3 +'foo'
	'3foo'
	> 'Colors: ' + [ 'red', 'green', 'blue' ]
	'Colors: red ,green,blue '
否则，两个运算数都会被转变成数字(参见11.2“转换成数字”)并相加:
	>3+1
	4
	>3+true
	4
这意味着你需要注意运算的顺序:
	> 'foo'+ (1 + 2)
	'foo3'
	> ('foo'+1) + 2
	'foo12'
●算法
  执行加法运算:
  value1 + value2
  会执行以下几个步骤。
  (1)确保两个运算数是原始值。obj对象通过内部的ToPrimitive(obj,Number)
  方法转变成原始值，这会调用obj. value0f ()方法或者obj . toString ()来实现。
  如果是日期对象,obj . toString()方法会优先被调用。
  (2)只要一个运算数是字符串,则将它们都转变为字符串并返回它们合并后的结果。
  (3)否则转变两个运算数为数字并返回它们的和。

9.6 布尔运算符和数字运算符
以下运算符只会有一种类型的运算数并且只返回该类型的运算结果。
布尔运算符。
●二元逻辑运算符
	x && y,x|| y
●逻辑非
	!x
数字运算符。
●算术运算符
	x+y,x-y,x*y,x/y,x%y
	++X,--x,x++,x--
	-x, +x
●位运算符
	~X
	x&y,xIy,x^y
	x<<y,x>>y,x>>>y

9.7 特殊运算符

9.7.1 条件运算符（？：）
条件运算符是一个表达式
"condition"? if_ true : if_ false
如果判断条件为true,则结果是if_ true; 否则，结果是if_ false.

9.7.2 逗号运算符
以下例子的第二个运算数会作为运算的结果返回:
	> 123， ' abc'
	'abc'
以下例子则演示了两个运算数的执行效果:
	>var x=0;
	> var y = (X++, 10);
	>x
	1
	>y
	10


9.8 通过typeof和instanceof判断值类型

9.8.1 typeof：判断原始值
typeof运算符用法:
	typeof  value
返回一个字符串，会描述value值的类型是什么。示例如下:
	> typeof undefined
	'undefined '
	> typeof ' abc'
	' string '
	> typeof {}
	' object '
	> typeof []
	'object'
typeof用来区分原始值和对象，并确定原始值(不能用instanceof处理的)。
●陷阱：typeof null
typeof null//object
●检测值是否存在
	typeof x==='undefined'
  有两个用处：
  （1）检测x是否为undefined
  （2）检测变量x是否存在

9.8.2 instanceof：检测对象是否是给定构造函数的实例
instanceof运算符:
value instanceof Constr
检测value是否是由构造函数Constr创建的或是否为它的一个子类。示例如下:
	> {} instanceof object
	true
	> [] instanceof Array // constructor of [ ]
	true
	> [] instanceof object // super -constructor of[]
	true
undefined和null调用instaneof的结果是false;
	>undefined instanceof object
	false
	> null instanceof Object
	false
但是对于其他的原始值也是false:
	> ' abc' instanceof 0bject
	false
	> 123 instanceof 0bject
	false

9.9 对象运算符
new (“构造函数-实例工厂”)
调用一个构造函数一示例:  new Point(3, 5)
delete (“删除属性”)
删除一个属性一示例: deleteobj.prop
in (“遍历和检测属性")
检测-一个对象是否包含一个给定的属性一示例: 'prop' in obj


第13章  语句

13.1 声明和变量赋值
var用来声明变量,你可以用它来创建变量并使用变量。其中等号运算符被用来给
变量赋值:
	var foo;
	foo = 'abc';
也可以把这两句整合为一个var语句:
	var foo='abc';
还可以将多个var语句合并为一个:
	var x，y=123， z;

13.3 循环
13.3.5 for-in
一个for-in循环如下:
        for (variablen inobject)
	statement
它可以遍历一个对象中的所有的键值，包含所继承的键值。如果某个键值是不可枚
举的，则会被忽略。下面是一些适用于for-in循环的规则。
●可以在循环中使用var声明变量，但是变量的作用域永远是当前包含循环的函数体。
●在遍历属性时，属性可以被删除。


















