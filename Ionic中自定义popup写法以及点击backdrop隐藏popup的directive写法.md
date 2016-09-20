> 网上提供的popup例子使用的是template，使用title、subtitle等参数来设置样式，但是这样在很多时候并不能满足我们的需求的（ionic自带样式本身就丑），所以这时候可以通过templateURL来写出我们想要的样式。

#效果

原样式：

![效果图](http://static.oschina.net/uploads/space/2016/0129/223817_YS13_2493500.png)

自定义后的样式（不敢说有多好看，但是至少还是有点进步的，这是之前在Windows下面截的图，其实在手机中会好很多）：

![原样式](http://static.oschina.net/uploads/space/2016/0129/223827_qEwL_2493500.png)

#实现

一．建立一个popup.html文件，里面编写自己想要的内容与效果，由于我这里就一个标题和input框，所以代码量较少。

``` html
<style>
.popup-title{
   padding-bottom: 10px;
}
</style>
<div>请填写快速按钮标题</div>
<input type="text" />

```

二．在JS中调用此popup文件，代码为：

``` javascript
$scope.myPopup = $ionicPopup.show({           
    templateUrl: 'templates/sale/modal/popup.html',
    scope: $scope,
    buttons: [{ //Array[Object] (可选)。放在弹窗footer内的按钮。
        text: '取消',
        type: 'sale-cancel',
        onTap: function(e) { 
            $scope.myPopup.close();  
        } 
    }, {
        text: '确定',
         type: 'sale-sure',
               onTap: function(e) { 
            console.log(e)
        }
    }]
});
```


可以看到，button里的内容也是可以自定义的，type对应的就是button的样式class，所以这里需要编写一个css文件，我修改了一下popup的border-radius，这个是需要覆盖源码的，请知悉。代码如下：

```css
.popup-container .popup {
    border-radius: 8px;
}
.popup-buttons {
    padding: 0;
    min-height: auto;
}
.sale-cancel {
    border-bottom-left-radius: 8px!important;
    margin-right: 0!important;
}
.sale-sure {
    border-bottom-right-radius: 8px!important;
    background-color: #EABA82;
    color: white!important;
}
```


注：使用!important的目的是使其样式优先级最高，并且在状态为active时不改变对应的样式。



#Directive

       在我个人的操作习惯来说，弹起这个弹窗的时候，如果我想取消本次弹窗，我是想点击这个弹窗的外部就可以取消，而不是说非要点击那个取消按钮，并且现在有很多的大屏手机，一般单手操作的话是不容易点击到取消的。

       但是在ionic的popup组件中是没有这样的效果的，所以我们需要自己去实现这个效果，这里使用directive来实现。
   

**一．在app.js中定义一个module来存储这个directive，如下：**

```javascript
var saleDrective = 
angular.module('saleDrective', ['saleDrective.directives']);
```

**二．建立js文件，加入以下代码：**

```javascript
angular.module('saleDrective.directives', []) 
  .directive('rjCloseBackDrop', [function() {
    return {     
        scope: false,
             restrict: 'A',
             replace: false,
             link: function($scope, iElm, iAttrs, controller) {       
            var htmlEl = angular.element(document.querySelector('html'));       
            htmlEl.on("click", function(event) {         
                if (event.target.nodeName === "HTML" & $scope.myPopup) {
                    $scope.myPopup.close();
                }       
            });     
        }   
    }; 
}])
```

这里的module名字需要与app.js定义的保持一致。Directive是直接操作dom的，判断当前popup为显示状态时（即下面的if语句，$scope.myPopup是在controller中定义popup名字，在上面代码中有写出来）点击外围backdrop即close当前popup。


**三．在HTML中引入这个directive**

在使用到这个popup对应的页面中的content上加入rj-close-back-drop如：

**四．在index中引入这个js文件**

现在就大功告成了，希望对大家有帮助。