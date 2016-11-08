> 相信很多微信开发者都会遇到过这样的问题：为了提高用户体验，把多个页面内容放在一个HTML页面进行展示，通过display属性以及transition动画来实现页面的跳转动画，但是点击微信顶部的“返回”按键之后就会直接跳出整个页面。


所以针对以上的情况，一般的应用的解决方案都是在页面的顶部自己画一个导航栏，需要点击自定义的导航栏来实现返回效果。

##弊端

但是不觉得和微信的导航栏放在一起，并且微信上面也有返回，两个返回很别扭吗？

并且对于安卓用户来说，底部的返回键岂不是没用了？万一手抖点到了怎么办？

没关系，使用history可以解决：

##History


我们知道，浏览器的跳转以及返回都是存在history历史里面的，它是栈，后进先出。
跳转一个新页面就进栈一个，返回一次就出栈一个。所以我们可以控制它的栈来实现返回的效果

##pushState
history提供了一个方法pushState，可以手动的添加页面进栈。

使用语法:history.pushState(state, title, url);

1. state：Object,与要跳转到的URL对应的状态信息。
2. title：页面名字，方便调试。
3. url：要跳转到的URL地址，不能跨域，对于单页应用来说没用，传空即可。

如：

```javascript
history.pushState({
     "page": "productList"
 }, "首页", "");
```

##replaceState
用新的state和URL替换当前。不会造成页面刷新。语法和pushState相同，该方法一般在首页使用，更改一下首页的参数名，方便监控和判断。

##onpopstate
history.go和history.back（包括用户按浏览器历史前进后退按钮）触发，并且页面无刷的时候（由于使用pushState修改了history）会触发popstate事件，事件发生时浏览器会从history中取出URL和对应的state对象替换当前的URL和history.state。通过event.state也可以获取history.state。

```javascript
window.onpopState = function(){
	var json = window.history.state;//获取当前所在的state
    if (json && json.page == "home") { //page即是pushState时设置的参数
        if (searchParams.sourceUrl && !selected) {
            history.go(-1);
        } else {
            document.querySelector('.product-page').removeCls('show');
            document.querySelector(".visit-hospital-page").classList.remove("show");
        }
    }}
```

##history.go
使用该方法即可实现出栈

```javascript
history.go(-1);//返回一个页面
history.go(-2);//返回两个页面
以此类推
```

结合以上所述，即可实现页面的无刷新跳转，希望对大家有帮助