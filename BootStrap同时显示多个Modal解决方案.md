> 使用BootStrap自带的Modal的时候，如果同时调用多个Modal，那么只能看到背景颜色加深但是看不见新的Modal页面。

问题主要是Modal的z-index有问题，重新计算z-index并赋值即可。在Modal完全加载之后调用方法：

```javascript
$(document).ready(function() {
    // 通过该方法来为每次弹出的模态框设置最新的zIndex值，从而使最新的modal显示在最前面
    $(document).on('show.bs.modal', '.modal', function() {
        var zIndex = 1040 + (10 * $('.modal:visible').length);
        $(this).css('z-index', zIndex);
        setTimeout(function() {
            $('.modal-backdrop').not('.modal-stack').css('z-index', zIndex - 1).addClass('modal-stack');
        }, 0);
    });
});
```