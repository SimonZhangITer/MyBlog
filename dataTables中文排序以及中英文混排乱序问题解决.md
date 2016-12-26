> dataTables是个强大的JQuery表格插件，但是不支持中文排序，这个比较令人头疼。

这里针对几种常见的数据格式写了一个dataTables的中文排序方法：

```javascript
//添加中文排序
function addChieseAsc() {
    jQuery.fn.dataTableExt.oSort['chinese-asc'] = function(x, y) {
        x = (x instanceof Array) ? x[0] : x == '-' ? 'Z' : x; //V最大
        y = (y instanceof Array) ? y[0] : y == '-' ? 'Z' : y;
        //javascript自带的中文比较函数，具体用法可自行查阅了解
        return x.localeCompare(y);
    };

    jQuery.fn.dataTableExt.oSort['chinese-desc'] = function(x, y) {
        x = (x instanceof Array) ? x[0] : x == '-' ? 'Z' : x;
        y = (y instanceof Array) ? y[0] : y == '-' ? 'Z' : y;
        return y.localeCompare(x);
    };

    // aTypes是插件存放表格内容类型的数组
    // reg赋值的正则表达式，用来判断是否是中文字符
    // 返回值push到aTypes数组，排序时扫描该数组，'chinese'则调用上面两个方法。返回null默认是'string'
    jQuery.fn.dataTableExt.aTypes.push(function(sData) {
        var reg = /^[\u4e00-\u9fa5]{0,}$/;
        if (reg.test(sData)) {
            return 'chinese';
        }
        return null;
    });
}
```

注:

- x,y可能是数组也可能是字符串，若是数组则x[0]即可获取数据
- x,y若不是数组，则需要判断字符串的具体类型，有可能是""、null等，这里我将这种类型的数据过滤成'-'了(方法在下面会给出)，所以这里判断是否是'-',如果是，在判断的时候视其为最大，这里用'Z'来代替
- 该方法需要在onReady之前引入。

这样还有一个问题就是：这里的方法只会识别中文，所以如果有的列是空字符串或者null和中文在一列的时候等是进入不了不了这个方法的，那么我们就需要手动将这一列设置为中文，跳转验证这一步。

设置方法为：

在dataTables初始化的时候传入type：

```javascript
$('#table').DataTable({
    columns: [{
        title: "test",
        type: 'chinese',//这里强制转换类型
        data: "fieldsName",
        render: function(data) {
            return formatTableData(data)
        }
    }],
});
function formatTableData(data) {
    switch (data) {
        case null:
        case "false":
        case "":
            return '-';
            break;
        case true:
            return '是';
            break;
        case false:
            return '否';
            break;
        default:
            return data;
            break;
    }
}
```

这样就大功告成了，dataTables可以对中文进行排序了，希望对大家有帮助~