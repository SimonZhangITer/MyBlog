> 通常情况下，我们使用document.title="标题名"来动态的修改标题，但是此方法在微信iOS端并没有用，查了不少资料，终于解决这个问题啦。现在把代码分享出来给大家

```javascript
function wxSetTitle(title) {
    document.title = title;
    var mobile = navigator.userAgent.toLowerCase();
    if (/iphone|ipad|ipod/.test(mobile)) {
        var iframe = document.createElement('iframe');
        iframe.style.visibility = 'hidden';
        iframe.setAttribute('src', 'loading.png');
        var iframeCallback = function() {
            setTimeout(function() {
                iframe.removeEventListener('load', iframeCallback);
                document.body.removeChild(iframe);
            }, 0);
        };
        iframe.addEventListener('load', iframeCallback);
        document.body.appendChild(iframe);
    }
}
```
直接调用此方法就可以啦