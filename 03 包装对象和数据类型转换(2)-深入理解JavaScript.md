深入理解JavaScript 第八章

8.2 原始值和对象
原始值包括布尔值、数字、字符串、null和undefined
其他值都是对象。

两者之间最主要的区别是类别内是如何相互比较的。
每一个对象有唯一的标识符并且只（严格）和自身相等；
var obj1={}; var obj2={}; obj1===obj2;//false
var obj3=obj1; obj1===obj3;//true
所有的原始值，只要编码值相同，则被认为相等
var prim1=123; var prim2=123; prim1===prim2;//true

8.2.1 原始值
特点
①按值进行比较：内容比较
3===3//true    'abc'==='abc'//true
②不可改变：其属性不能被改变、添加或移除
③固定类型的组合：不能自定义原始值

8.2.2 对象
简单对象：可以通过对象字面量来创建{name：'jane',age：20}
数组：可以通过数组字面量来创建 [1,2,3]
正则表达式：可以通过正则表达式字面量来创建
特点：
①按引用进行比较：每个值都有各自的身份标识，比较引用时会比较对象的身份标识
var obj1={}; var obj2={}; obj1===obj2;//false
var obj3=obj1; obj1===obj3;//true
②默认可变：对象属性可以很自由的被改变、添加或移除
③用户可扩展：构造函数可以被看作是自定义类型的补充。

8.4 原始值的包装对象
布尔值、数字和字符串这三种原始值都有相应的构造函数：Boolean,Number,String。
它们的实例（称为包装对象）包含原始值。这些构造函数有两种用法：
①作为构造函数，他们创建的对象和他们包装的原始值有很大的不同。
typeof new String('abc')//'object'
new String('abc')==='abc'//false
②作为函数，它们会将值转换为相应的原始值。
String(123)//'123'

8.4.1 包装对象不同于原始值
'abc'===new String('abc') //false
包装实例是对象，在JavaScript中没有比较对象的方法，即使是通过宽松相等
var a=new String('abc');
var b=new String('abc');
a==b//false

8.4.2 原始值的包装与去包装
有一种使用包装对象的场景：在需要对一个原始值增加属性时，首先要对这个原始值进行包装并且给包装后的对象增加属性，而当要使用值之前需要先对它进行去包装
通过调用包装构造函数来对原始值进行包装：
new Boolean(true) new Number(123) new String('abc')
通过调用valueOf()来对原始值进行去包装。
new Boolean(true).valueOf()//true
new Number(123).valueOf()//123
new String('abc').valueOf()//'abc'
将包装对象转换为原始值时只能提取出数字和字符串，而布尔值则不能
Boolean(new  Boolean(false))//true
Number(new Number(123))//123
String(new String('abc'))//'abc'

8.4.3 原始值从包装器借调方法
原始值没有私有方法，但是它们会从各自的包装器中借调方法
'abc'.charAt===String.prototype.charAt//true
宽松模式和严格模式会以不同的方式处理“借调”过程。在宽松模式中，原始值会在运行过程中转换为包装器
在严格模式中，对包装器原型方法的调用是透明的

8.5 强制类型转换
强制类型转换指的是值从一种类型隐式的转换成另一种类型。

8.5.1 强制类型转换会隐藏bug
程序接受的用户输入为字符串，即使输入的是一个数字也没关系。
如果将一个字符串类型的数字作为数字使用也不会得到警告。

8.5.2 转换成布尔值、数字、字符串和对象的函数
Boolean()转换为布尔值
undefined、null、0、NaN、''(空字符串)被转换为false，其他的值会转换为true（包括所有的对象）

Number()转换为数字
undefined会转换成NaN。
null会转换成0。
false会转换成0，true 会转换成1。
字符串会被解析。.
对象会先转换为原始值，然后再转换为数字。

String()转换为字符串
对象会先转换为原始值，然后再转换为字符串

Object()
对象会转换为它们自身，undefined和null会转换成空对象，而原始值会转换为包装后的原始值。
Object(undefined)//{}
Object('abc') instanceof String//true





