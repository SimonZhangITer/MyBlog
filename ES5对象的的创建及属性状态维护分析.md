
在说属性之前，我们先来了解一下ES5的新方法,Object.create()函数。
#新的对象创建方法
在旧的“原型继承”观念中，它的本质上是“复制原型”，即：以原型为模板复制一个新的对象。然而我们应该注意到一点事实：在这个思路上，“构造器函数”本身是无意义的。更确切的说，构造器函数对实例的修饰作用可有可无，例如：

``` javascript
//在构造器中修饰对象实例
function MyObject(){
	this.yyy = ...;
}
```
当意识到这一点后，ES5实现Object.cerate()这样一种简单的方法，通过这一方法将“构造器函数”从对象创建过程中赶了出去。在新的机制中，对象变成了简单的“原型继承+属性定义”，而不再需要“构造器”这样一层语义，例如：

``` javascript
//新的对象创建方法
newObj = Object.create(prototypeObj,PropertyDescriptors);
```
这里的PropertyDescriptors是一组属性描述符，用于声明基于prototypeObj这个原型之上的一些新的属性添加或修改，它与defineProperties()方法中的props参数是一样的，并在事实上也将调用后者。它的用法如下例所示：

``` javascript
var aPrototypeObject = {name1:"value1"};
var aNewInstance = Object.create(aPrototypeObject,{
	name2:{value:'value2'},
	name3:{get:function(){ return 'value3' }}
})
```

很显然，在这种新方案中我们看不到类似MyObject()那样的构造器了。事实上在引擎实现Object.create()时也并不特别地声明某个构造器。

**<font color=red >所以，所有由Object.create()创建的对象实例具有各自不同的原型（这取决于调用create()方法时传入的参数）,但它们的constractor值指向相同的引用——引擎内建的Object构造器。</font>**

#属性状态维护
ES5中在Object()上声明了三组方法，用于维护对象本身在属性方面的信息，如下表（Markdown不会使用分组列表，大家凑合看看。。如果有知道的也告诉我一下哈~）

| 分类        | 方法           | 说明  |
| ------------- |:-------------:| -----|
|   取属性列表      | getOwnPropertyNames(obj) | 取对象自有的属性名数组 |
| 取属性列表    | keys(obj)      |   取对象自由的、可见的属性名数组 |
|  状态维护 |   preventExtensions(obj)    |   使实例obj不能添加新属性 |
| 状态维护 | seal(obj) |使实例obj不能添加新属性，也不能删除既有属性 |
| 状态维护 | freeze(obj) | 使实例obj所有属性只读，且不能再添加、删除属性 |
| 状态检查 | isExtensible(obj) | 返回preventExtensions状态 |
| 状态检查 | isSealed(obj) | 返回seal状态 |
| 状态检查 | isFrozen(obj) | 返回freeze状态  |
其中，preventExtensions、seal和freeze三种状态都是针对对象来操作的，会影响到所有属性的性质的设置。需要强调的有两点：

* 由原型继承来的性质同样会受到影响
* 以当前对象作为原型时，子类可以通过重新定义同名属性来覆盖这些状态

更进一步的说，这三种状态是无法影响子类使用defineProperty()和defineProperties()来“重新定义（覆盖）”同名属性的。

本质上说，delete运算是用于删除运算对象属性的属性描述符，而非某个属性。

##取属性列表

取属性列表的传统方法是使用for...in语句。为方便后续讨论，我们先为该语句封装一个与Object.keys()类似的方法：

``` javascript
Object.forIn = function(obj){
	var Result = [];
	for(var n in obj) Result.push(n);
	return Result;
}
```

forIn()得到的总是该对象全部可见的属性列表。而keys()将是其中的一个子集，即<font color=red>"自有的(不包括继承而来的)"可见属性列表。</font>下面的例子将显示二者的不同：

``` javascript
var obj1 = {n1:100};
var obj2 = Object.create(obj1,{n2 : {value :200,enumerable:true}});

//显示'n1' , 'n2'
//  - 其中n1继承自obj1
alert(Object.forIn(obj2));

//显示'n2'
alert(Object.keys(obj2));
```
getOwnPropertyNames()得到的与上述两种情况都不相同。它列举全部自有的属性，但无论它是否可见。也就是说，<font color=red>它是keys()所列举内容的超集，包括全部可见和不可见的、自有的属性。</font>仍以上述为例：

``` javascript
// (续上例)

//定义属性名n3,其enumerable性质默认为false
Object.defineProperty(obj2,'n3',{value:300})

//仍然显示'n1','n2'
// - 新定义的n3不可见
alert(Object.forIn(obj2));

//显示'n2'
alert(Object.keys(obj2));

//显示n2,n3
alert(Object.getOwnPropertyNames(obj2));

```
##使用defineProperty来维护属性的性质
在defineProperty()或defineProperties()中操作某个属性时，如果该名字的属性未声明则新建它；如果已经存在，则使用描述符中的新的性质来覆盖旧的性质值。

这也意味着一个使用"数据属性描述符"的属性，也可以重新使用"存取属性描述符"——但总的来说只能存在其中一个。例如：

``` javascript
var pOld,pNew;
var obj = { data : 'oldValue'}

//显示'value,writable,enumerable,configuable'
pOld = Object.getOwnPropertyDescriptor(obj,'data');
alert(Object.keys(pOld));

//步骤一：通过一个闭包来保存旧的obj.data的值
Object.defineProperty(obj,'data',function(oldValue){
	return {
		get:function(){ return oldValue},
		configurable:false
	}
}(obj.data))

//显示'get,set,enumerable,configurable'
pNew = Object.getOwnPropertyDescriptor(obj,'data');
alert(pNew);

//步骤二：测试使用重定义的getter来取obj.data的值
// - 显示 'oldValue'
alert(obj.data);

//步骤三：（测试）尝试再次声明data属性
// - 由于在步骤一中已经设置configurable为false，因此导致异常(can't redefine)。
Object.defineProperty(obj,'data',{value:100});
```
##对于继承自原型的属性，修改其值的效果

如果某个从原型继承来的属性是可写的，并且它使用的是"数据属性描述符"，那么在子类中修改该值，将隐式地创建一个属性描述符。这个新属性描述符将按照"向对象添加一个属性"的规格来初始化。即：必然是数据属性描述符，且Writable,Enumerable和Configurable均为true值。例如：

``` javascript
var obj1 = {n1 : 100};
var obj2 = Object.create(obj1);

//显示为空
// - 重置n1的enumerable性质为false，因此在obj1中是不可见的
Object.defineProperty(obj1,'n1',{enumerable:false})
alert(Object.keys(obj1));

//显示为空
// - n1不是obj2的自有属性
alert(Object.getOwnPropertyNames(obj2));

//显示n1
// - 由于n1赋值导致新的属性描述符，因此n1成为了自有的属性
obj2.n1 = 'newValue';
alert(Object.getOwnPropertyNames(obj2));

//显示n1,表明n1是可见的
// - 由于新的属性描述符的enumerable重置为true，因此在obj2中它是可见的
alert(Object.keys(obj2));
```
如果一个属性使用的是"存取属性描述符",那么无论它的读写性为何，都不会新建属性描述符。对子类中该属性的读写，都只会忠诚地调用（继承而来的、原型中的）读写器。

##重写原型继承来的属性的描述符
使用defineProperty()或defineProperties()将重新定义该属性，会显式的创建一个属性描述符。在这种情况下，该属性也将变成自雷对象中"自有的"属性，它的可见性等性质就由新的描述符来决定。

与上一小节不同的是，这与原型中该属性是否"只读"或是否允许修改性质(configurable)无关。

这可能导致类似如下的情况：在父类中某个属性时只读的，并且不可修改其描述符性质的，但是在子类中，同一个名字的属性却可以读写并可以重新修改性质。更为严重的是，仅仅观察两个对象实例的外观，我们无法识别这种差异是如何导致的。下面的示例说明这种情况：

``` javascript
var obj1 = {n1 : 100};
var obj2 = Object.create(obj1);

//对于原型对象obj1,修改其属性n1的性质，使其不可列举、修改、且不能重设性质
Object.defineProperty(obj1,'n1',{writable:false,enumerable:false,configurable:false});

//显示为空,obj1.n1是不可列举的
alert(Object.keys(obj1));

//由于不可重设性质，因此对obj1.n1的下述调用将导致异常
//Object.defineProperty(obj1,'n1',{configurable:true});
```
接下来我们观察"重新定义属性"带来的效果：

``` javascript
//(续上例)

//重新定义obj2.n1
Object.defineProperty(obj2,'n1',{value:obj2.n1,writable:true,enumerable:true,configurable:true});

//显示newValue'
// - 结论:可以通过重定义属性，使该属性从"只读"变成"可读写"（以及其他性质的变化）
obj2.n1 = 'newValue';
alert(obj2.n1);

//列举obj2的自有性质，结果显示：n1
// - 现在n1是自有的属性了
alert(Object.getOwnpropertyNames(obj2));
```
从表面上看，一个父类中只读的属性在子类变成了可读写。而且，一旦我们用delete删除该属性，它又会恢复父类中的值和性质。例如：

``` javascript
//尝试删除该属性
// - 显示100，即它在原型中的值
delete obj2.n1;
alert(obj2.n1);
```
再次强调这一事实：<font color=red>在ES5中没有任何方法可以阻止上述过程。也就是说，我们无法阻止子类对父类同名属性的重定义，也无法避免这种重定义可能带来的业务逻辑问题。</font>
