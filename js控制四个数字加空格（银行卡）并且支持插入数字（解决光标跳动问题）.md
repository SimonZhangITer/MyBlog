> 一般来说，为了便于用户输入，银行卡号会每四个数字用空格隔开，这个在前端使用正则表达式不难做到，但是容易出现不能从数字中间插入的问题：光标移动到中间，输入一个数字后光标便自动跳转到最右了。

上述问题非常影响用户体验，经过一段时间的研究，我用以下办法解决了此问题：

##Input

这里使用了type="tel",这样是为了在移动端调出数字键盘，H5的新特性pattern也可以实现，但是这个属性的兼容性并不是很好。

```html
<input type="tel" placeholder="请输入储蓄卡卡号" name="cardNum">
```

##监控输入

js针对input有个属性<font color=red>selectionStart</font>，这个是当前光标所在的位置，那么我们只要获取了这个位置，哪怕之后它跳到了最右，我们也可以控制它回到原来的位置。

```javascript
//监控input事件
document.querySelector('input[name=cardNum]').addEventListener('input', function() {
    //获取当前光标位置
    var position = this.selectionStart;
    var v = this.value;
    //四个字符加一个空格
    this.value = v.replace(/\s/g, '').replace(/(.{4})/g, "$1 ");
    //优化语句：如果当前位置是空格字符，则自动清除掉
    if (this.value.charAt(this.value.length - 1) == ' ') {
        this.value = this.value.substring(0, this.value.length - 1);
    }
    var input = this;
    //重新定位光标位置，start和end都需要设置，不然就是截取片段了
    input.selectionStart = position + countSpace(this.value, position);
    input.selectionEnd = position + countSpace(this.value, position);
})
```
