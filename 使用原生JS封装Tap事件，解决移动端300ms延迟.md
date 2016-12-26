> 为了防止误操作，移动端iOS操作系统针对原生click事件做了300ms的延迟，这在一定程度上影响了我们的使用体验。

#FastClick

现在有现成的插件fastclick可以解决这个问题，但是也有弊端：

- GitHub上最新版本的插件大小为25.4kb，轻量为趋势，能省则省。
- 它的核心思想是取消默认的click时间，判断当前dom节点的类型进行相应的操作，这个判断过程较为繁琐。

#MyTapEvent

本人最近在做微信项目，由于fastclick插件存在一定弊端，因此开发了一个简单的tap事件，主要思想有以下几点：

##Thinking

- 一次tap事件包含touchstart和touchmove(轻微移动)以及touchend三种状态
- callback方法在touchend后执行
- 根据chrome浏览器默认的判断取消点击的移动量，手指偏移量（水平或垂直）超过15px则判定为滚动，取消执行tap事件
- 手指按下时间过长不视为点击，默认时间间隔为500ms
- 使用HTMLElement来扩充原型，方便添加Event
- 使用单例模式，确保只加载一次

ok，思想定下来，代码写起来就清晰多了：

```javascript
if (!HTMLElement.prototype.addTapEvent) {
    HTMLElement.prototype.addTapEvent = function(callback) {
        var tapStartTime = 0,
            tapEndTime = 0,
            tapTime = 500, //tap等待时间，在此事件下松开可触发方法
            tapStartClientX = 0,
            tapStartClientY = 0,
            tapEndClientX = 0,
            tapEndClientY = 0,
            tapScollHeight = 15, //水平或垂直方向移动超过15px测判定为取消（根据chrome浏览器默认的判断取消点击的移动量)
            cancleClick = false;
        this.addEventListener('touchstart', function() {
            tapStartTime = event.timeStamp;
            var touch = event.changedTouches[0];
            tapStartClientX = touch.clientX;
            tapStartClientY = touch.clientY;
            cancleClick = false;
        })
        this.addEventListener('touchmove', function() {
            var touch = event.changedTouches[0];
            tapEndClientX = touch.clientX;
            tapEndClientY = touch.clientY;
            if ((Math.abs(tapEndClientX - tapStartClientX) > tapScollHeight) || (Math.abs(tapEndClientY - tapStartClientY) > tapScollHeight)) {
                cancleClick = true;
            }
        })
        this.addEventListener('touchend', function() {
            tapEndTime = event.timeStamp;
            if (!cancleClick && (tapEndTime - tapStartTime) <= tapTime) {
                callback();
            }
        })
    }
}
```

##Usage

```javascript
HTMLElement.addTapEvent(function(){
	//do something...
})
如：
document.querySelect('#test').addTapEvent(function(){
	alert('this is a tap event');
})
```


##Case

这里给一个移动端案例，同时也包含了闭包的知识，前20项为tap事件，后30项为click事件，大家可以在手机上试一下效果，感受一下两种方法的差别：

```html
<style media="screen">
    li {
        padding: 20px;
    }

</style>
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
        <meta name="apple-mobile-web-app-capable" content="yes">
        <meta name="apple-touch-fullscreen" content="yes">
        <meta name="apple-mobile-web-app-status-bar-style" content="black">
        <meta name="format-detection" content="telephone=no,email=no">
        <meta name="x5-orientation" content="portrait">
        <title>test</title>
    </head>
    <body>
        <ul></ul>
    </body>
    <script type="text/javascript">
        if (!HTMLElement.prototype.addTapEvent) {
            HTMLElement.prototype.addTapEvent = function(callback) {
                var tapStartTime = 0,
                    tapEndTime = 0,
                    tapTime = 500, //tap等待时间，在此事件下松开可触发方法
                    tapStartClientX = 0,
                    tapStartClientY = 0,
                    tapEndClientX = 0,
                    tapEndClientY = 0,
                    tapScollHeight = 15, //水平或垂直方向移动超过15px测判定为取消（根据chrome浏览器默认的判断取消点击的移动量)
                    cancleClick = false;
                this.addEventListener('touchstart', function() {
                    tapStartTime = event.timeStamp;
                    var touch = event.changedTouches[0];
                    tapStartClientX = touch.clientX;
                    tapStartClientY = touch.clientY;
                    cancleClick = false;
                })
                this.addEventListener('touchmove', function() {
                    var touch = event.changedTouches[0];
                    tapEndClientX = touch.clientX;
                    tapEndClientY = touch.clientY;
                    if ((Math.abs(tapEndClientX - tapStartClientX) > tapScollHeight) || (Math.abs(tapEndClientY - tapStartClientY) > tapScollHeight)) {
                        cancleClick = true;
                    }
                })
                this.addEventListener('touchend', function() {
                    tapEndTime = event.timeStamp;
                    if (!cancleClick && (tapEndTime - tapStartTime) <= tapTime) {
                        callback();
                    }
                })
            }
        }

        var ul = document.querySelector('ul');
        for (var i = 1; i <= 20; i++) {
            var li = document.createElement('li');
            li.innerHTML = i;
            li.addTapEvent(function() {
                var x = i;
                return function() {
                    alert(x);
                }
            }())
            ul.appendChild(li);
        }
        for (var j = 21; j <= 50; j++) {
            var li = document.createElement('li');
            li.innerHTML = j;
            li.onclick = function() {
                var x = j;
                return function() {
                    alert(x);
                }
            }()
            ul.appendChild(li);
        }
    </script>
</html>

```