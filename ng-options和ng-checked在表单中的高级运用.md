> AngularJS是当前非常的流行的前端框架，它的语法糖非常多，也极大的方便了前端开发者，但是有着用法还是需要去琢磨一下的


#ng-options
在select表单控件中，总结一下目前的几种写法。

##普通写法

```html
<select>
	<option value="test1">test1</option>
	<option value="test1">test1</option>
	<option value="test1">test1</option>
	<option value="test1">test1</option>
</select>
```
优点：简单

缺点：

- 代码很不简洁，如果选项较多就会很乱
- 不方便渲染，如果option在变需要使用js动态加载
- 不方便存储对象

##使用ng-repeat

ng-repeat是angularJS中非常强大的一个directive,在渲染列表上极大的方便了前端开发者，那么由于有多个重复的option,当然可以使用ng-repeat，用法如下：

```html
<select>
	<option ng-repeat="option in options" value="{{option}}">{{option.name}}</option>
</select>
<script>
	$scope.options = [{id:1,name:'test1'},{id:2,name:'test2'},{id:3,name:'test3'}];
</scirpt>
```

优点：

- 代码简介
- 可存储对象，取值方便

缺点：

- <font color=red>没有默认显示！</font>,在有些界面需求中，select可能是需要placeholder一样的显示提示效果的，那么使用这个方式显示效果默认是<font color=red>空白</font>
- 无法通过ng-model来获取当前选择的值

##使用ng-options

这里使用一个年级、班级的选项来作为例子：即选择年级之后再显示对应的可选班级。

```html
<select ng-model="modal.grade" ng-change="modalChangeGrade()" ng-options="grade.gradeText for grade in modal.grades">
   <option value="" disabled>请选择</option>
</select>
<script>
	$scope.modal.grades = [
	{id:1,gradeText:'初一',classes:[]},
	{id:2,gradeText:'初二',classes:[]},
	{id:3,gradeText:'高一'},classes:[]];
	$scope.modalChangeGrade = function(){
		//班级的HTML片段就不在这里写了
		$scope.modal.classes = $scope.modal.grade.classes;
	}
</scirpt>
```
注：

- "请选择"的option需要有value，不然会报错
- 如果要设置默认选择值，比如一开始就选择"高一"，则需要设置modal在数组里的对象。

```javascript
$scope.modal.grade = $scope.modal.grades[2];//高一在数组的位置角标为2
```

优点：

- 代码简洁，易于维护
- <font color=red>有默认显示</font>
- 可以使用ng-modal准确获取当前选择的对象

#ng-checked

checkbox和radio是我们经常使用到的表单组件，那么如何使用angularJs简洁方便的获取当前已选择对象呢？

这里只说angularJs的用法：

下面依然以年级和班级为例：

```html
<div ng-repeat="class in grade.classes" ng-click="class.is_checked=!class.is_checked">
   <input type="checkbox" value="" ng-checked="class.is_checked">
   {{class.id+'班'}}
</div>
```
最后需要查看有哪些checkbox被选中时，只需要遍历$scope.grade.classes数组查看有哪些对象的is_checked属性为true即可。

radio的用法同理。