> Datatables是一款jquery表格插件。它是一个高度灵活的工具，可以将任何HTML表格添加高级的交互功能。

#主要功能

- 分页，即时搜索和排序
- 几乎支持任何数据源：DOM， javascript， Ajax 和 服务器处理
- 支持不同主题 DataTables, jQuery UI, Bootstrap, Foundation
- 各式各样的扩展: Editor, TableTools, FixedColumns ……
- 丰富多样的option和强大的API
- 支持国际化
- 超过2900+个单元测试
- 免费开源 （ MIT license ）！ 商业支持
- 更多特性请到官网查看

这里主要讲一下DataTable使用Ajax来获取数据并且动态加载dom的方法。这样也方便了数据管理，也避免了HTML页面中大量的tr，看起来很凌乱。

#显示效果

![](https://static.oschina.net/uploads/space/2016/1108/102903_MNNM_2493500.png)

#源文件

首先需要引入dataTables的源文件：

CSS:  [http://cdn.datatables.net/1.10.12/css/jquery.dataTables.min.css](http://cdn.datatables.net/1.10.12/css/jquery.dataTables.min.css)

JS: [http://cdn.datatables.net/1.10.12/js/jquery.dataTables.min.js](http://cdn.datatables.net/1.10.12/js/jquery.dataTables.min.js)


#使用

##HTML

只需要定义一个table，给一个唯一的标志（id或者唯一的class）；

```html
<table class="table table-bordered table-striped" id="dailyTable"></table>
```

##JS

在页面元素渲染完成之后，获取table，使用dataTables对其操作。

- ajax是数据的url，这里用的本地数据，使用的是相对路径。当然，也可以使用HTTP请求
- columns是一个数组，定义的是表格的标题，数组有多少个界面就会显示多少。格式必须是{title:"xxx"}

```javascript
$(document).ready(function($) {
    $('#dailyTable').DataTable({
        ajax: "../json/tableData.json",
        columns: [{
            title: "Rendering engine"
        }, {
            title: "Browser"
        }, {
            title: "Platform(s)"
        }, {
            title: "Engine version"
        }, {
            title: "CSS grade"
        }]
    });
});
```

##JSON

这里数据太多，只说明一下格式。

json数据格式为二维数组,其数据需要被包裹在"data"字段下面。每个二维数组的数据按照js中的title顺序依次排列。


```json
{
    "data": [
        [
            "Tiger Nixon",
            "System Architect",
            "Edinburgh",
            "5421",
            "2011/04/25",
            "$320,800"
        ],
        [
            "Garrett Winters",
            "Accountant",
            "Tokyo",
            "8422",
            "2011/07/25",
            "$170,750"
        ]
   ]
}
```