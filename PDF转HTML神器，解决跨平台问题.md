> 在手机端展示PDF是移动开发者的一大痛点

目前在PC端展示PDF有较多的解决方案，比如：

#PC端

##embed标签

```html
<embed type="application/pdf" src="test.pdf" width="100%" height="100%"/>
```

##pdf.js

这里不再赘述使用方法，网上一大堆


那么在手机端展示pdf就成了一个难题，尤其是在微信上（兼容性无力吐槽），经过一番研究，在GitHub上发现了pdf2htmlEX这个神器！

#移动端

##pdf2htmlEX

GitHub链接先来一发：[https://github.com/coolwanglu/pdf2htmlEX](https://github.com/coolwanglu/pdf2htmlEX)

Demo再来一发，注意url，是xxx.html! [http://coolwanglu.github.io/pdf2htmlEX/demo/geneve.html](http://coolwanglu.github.io/pdf2htmlEX/demo/geneve.html)

ok,相信你看了Demo基本已经被它的显示效果惊呆了，下面我们来看一下它的使用吧：

##安装

官方文档：[https://github.com/coolwanglu/pdf2htmlEX/wiki/Building](https://github.com/coolwanglu/pdf2htmlEX/wiki/Building)

Mac OS X可以使用brew来安装

```bash
brew install pdf2htmlEX
```

Windows在以上链接最底部有下载链接。

##使用

官方文档：[https://github.com/coolwanglu/pdf2htmlEX/wiki/Quick-Start](https://github.com/coolwanglu/pdf2htmlEX/wiki/Quick-Start)

安装好pdf2htmlEX后，使用命令行切换到pdf文件目录。

这里简单介绍几个命令：

###全部转换为HTML

```bash
pdf2htmlEX --zoom 1.3 pdf/test.pdf
```

- zoom表示缩放 后面的数字就是缩放比例，数字越小，生成的HTML显示界面越小，但是本身的bytes大小不受影响
- pdf/test.pdf是文件路径，相对于当前目录的路径
- 生成的HTML文件就在当前目录下，文件名不变。

###全部转换为目录(HTML,CSS,IMGS,FONTS)

```bash
pdf2htmlEX --embed cfijo --dest-dir out pdf/test.pdf
```

- embed cfijo没有实际意义，是本身命令，不需要修改
- out是自定义文件夹名称，所有的转换的文件都生成在此目录
- pdf/test.pdf是源pdf文件


除了以上的命令以外，还有很多实用方式，比如截取片段转换、修改像素等，这里不再赘述，感兴趣的可以去官网查看。
