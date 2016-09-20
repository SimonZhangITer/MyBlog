#【ionic框架bug】slidebox在使用model或popover后width变为0的解决方案

##问题描述
> 如果在silideBox上使用缓存，那么在进入另一个state之后，打开一个Popover或者一个Modal，再返回原来的页面，则slideBox的width变为0，即看不见silideBox的内容了。

#解决方案
###一.使用ng-if
即不使用缓存，但是这样的弊端就是没有缓存-.-，其实原理就是每次重新渲染slidebox。

###二.使用$ionicSlideBoxDelegate.update()

在每次进入页面的时候执行此方法，会让slideBox重新计算宽度和高度。判断进入页面的方法可以是监听\$ionicView.enter或者$stateChangeSuccess，建议后者，其中封装的有stateFrom、stateTo等方法方便判断页面的跳转，并且反应较前者快很多。

###三.修改ionic源码（建议）

为什么说这个是ionic的框架bug，就是因为它的源码写的有缺陷，导致一些莫名其妙的问题。所以打开ionic.bundle.js查看它的源码（只展示一开始的一部分）：

``` javascript
ionic.views.Slider = ionic.views.View.inherit({
            initialize: function(options) {
                    varslider = this;
                    utilities
                    varnoop = function() {};
                    simplenooperationfunction
                    varoffloadFn = function(fn) {
                        setTimeout(fn || noop, 0);
                    };
                    offloadafunctionsexecution

                    checkbrowsercapabilities
                    varbrowser = {
                        addEventListener: !!window.addEventListener,
                        touch: ('ontouchstart'
                            inwindow) || window.DocumentTouch && documentinstanceofDocumentTouch,
                        transitions: (function(temp) {
                            varprops = ['transitionProperty', 'WebkitTransition', 'MozTransition', 'OTransition', 'msTransition'];
                            for (var i in props)
                                if (temp.style[props[i]] !== undefined) returntrue;
                            return false;
                        })(document.createElement('swipe'))
                    };
                    var container = options.el;
                    //quitifnorootelement
                    if (!container) return;
                    var element = container.children[0];
                    var slides, slidePos, width, length;
                    options = options || {};
                    var index = parseInt(options.startSlide, 10) || 0;
                    var speed = options.speed || 300;
                    options.continuous = options.continuous !== undefined ? options.continuous : true;

                    function setup() {

                        //此为添加的代码
                        console.log('setup')
                        if (!container.offsetWidth) {
                            return;
                        }
                        //此为添加的代码end
                        //cacheslides
                        slides = element.children;
                        length = slides.length;
                       
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;阅读源代码后可以发现，setup方法是slidebox的主方法，所以我在setup的方法里面加了一个打印语句，看他在什么时间会执行这个方法。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;测试期间，发现每当popover或者modal打开的时候，这个方法都会执行两次（原因不明），代码中：<font color=red>container返回的是整个slider的HTML代码片段，当他在页面中可见的时候，它的可见宽度大于0，反之为0，所以在我们跳转至其他页面的时候，它的可见宽度即变为0，这个时候我们只要阻止它改变当前的状态即可，所以加上return，这样问题就解决啦。</font>

###四.直接引入修改好的js片段，覆盖原来的slidebox代码

需要注意的是，在index页面的引入顺序，sliderView.js一定要在ionic.bundle.js之后引入，这样才能实现覆盖。

下载链接<https://github.com/SimonZhangITer/ionic_slidebox/blob/master/slidebox.js>