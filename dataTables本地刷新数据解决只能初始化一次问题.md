> dataTables的表格只能初始化一次，这样如果需要动态改变表格数据的话就需要写多个表格，这样很显然不是一个好的解决方案。

dataTables Api提供了刷新数据解决方案：

这里大概说一下案例：在一个页面中有多个tab，每个tab对应一个表格，表格头都是一致的，只是内容不一致。为了提高接口效率，后台会提前请求数据存在node中，前端是一次性请求全部数据，所以表格数据的刷新是本地数据的填充。

###初始化代码：

```javascript
$(document).ready(function($) { 
    //初始化配置
    $('#detailTable').dataTable({
        paging: false,
        searching: false,
        info: false,
        columns: [{ //根据接口传过来的字段进行设置
            data: "name"
        }, {
            data: "department"
        }, {
            data: "dsm"
        }, {
            data: "signin_time"
        }, {
            data: "questionaire_time"
        }]
    });
});
```
###刷新数据代码

```javascript
//初始化会议详情数据
function initDetailTableData(dataArr) { //dataArr是表格数据数组，和初始化配置需一样的结构
    var table = $('#detailTable').dataTable();
    var oSettings = table.fnSettings(); //这里获取表格的配置
    table.fnClearTable(this); //动态刷新关键部分语句，先清空数据
    for (var i = 0, l = dataArr.length; i < l; i++) {
        table.oApi._fnAddData(oSettings, dataArr[i]); //这里添加一行数据
    }
    oSettings.aiDisplay = oSettings.aiDisplayMaster.slice();
    table.fnDraw();//绘制表格
}
```