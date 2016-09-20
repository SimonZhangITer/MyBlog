> 相信喜欢使用MarkDown的胖友们一定遇到过这样的情况：在某网站看到一个很不错的总结表格，想要引用一下，但是复制下来发现MarkDown并不支持，自己画出来又太浪费时间，是不是很头疼？今天发现一个办法，完美解决此问题，贼嗨！

原理是Markdown支持HTML语法，所以我们只需要把表格转成HTML即可。

打开这个网站 <http://pressbin.com/tools/excel_to_html_table/index.html>

<font color=15962f>把表格直接完全粘贴到上面的框中，按下"convert"，下面就生成了HTML格式的代码,复制到Markdown编辑器即可。</font>

有几点需要说明的：

1. 将第一个<font color=#f743d3>\<table></font>变成<font color=#f743d3>\<table class="table table-bordered table-striped table-condensed"></font>

	为什么要加它们呢？这三个是什么意思呢？解释如下：
	
	它们都会给表格带上某种样式，如果没有的话，比较不好看
	
	* table-bordered：带圆角边框和竖线
	* table-striped：奇偶行颜色不同
	* table-condensed：压缩行距
	
	以上三个可以进行不同的组合，如果是很长的表格，建议只用后两个。
2. 另外，如果需要表头跟内容不一样，可以将<font color=#f743d3>\<td>表头内容\</td></font>换成<font color=#f743d3>\<th>表头内容\</th></font>。
3. 如果表格内文需要换行，可以在要换行的内容后加入<font color=#f743d3>\<br></font>，后面的内容就会跑到下一行。
4. 如果内文中有代码，需要特别显示，可使用：<font color=#f743d3>\<code>代码\</code></font>。
5. 如果表格中有需要设为斜体的内容，可使用：<font color=#f743d3>\<I>要设为斜体的内容\</I></font>。
6. 如果有跨行或者跨列的单元格，可用<font color=#f743d3>\<th colspan="跨列数">内容\</th></font>。
跨行则是用<font color=#f743d3>rowspan="跨行数"</font>。
7. 如果要调整某一列的宽度，可使用：<font color=#f743d3>\<th width="宽度值或百分比">表头内容\</th></font>。
8. 如果要调整整个表格的宽度，可以参考berlinix的这篇文章：<http://www.ituring.com.cn/article/details/8367>

