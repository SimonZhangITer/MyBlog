> 直接在Form表单中提交请求会自动跳转到其他页面，这在一定程度上不符合业务需求，不过使用iframe内嵌的方式也可以解决这个问题，但是如果需要获取接口返回的数据，那么存放在iframe中就不是很方便了。

没关系，Ajax可以解决：

- 需要引入JQuery

HTML：

```html
<form id="formID">
    <input type="file"/>
    <button onclick="submitData()">提交</button>
</form>
```
注意：

- form标签上不需要有action，target,method等方法，在Ajax里面会重新声明
- 提交按钮不要是type="submit"，不然会触发默认提交方法

JavaScript:

```javascript
function submitData() {
    var formData = new FormData($("#formID")[0]);
    $.ajax({
        url: '/api/testUrl',
        type: 'POST',
        data: formData,
        async: false,
        cache: false,
        contentType: false,
        processData: false,
        success: function(returndata) {
            //成功返回的回调
        },
        error: function(returndata) {
            //接口错误的回调
        }
    });
};
```

注意：

- 在错误返回的回调中，returndata是经过JQuery处理的数据对象，如果要获取接口返回数据，returndata.responseJSON即可