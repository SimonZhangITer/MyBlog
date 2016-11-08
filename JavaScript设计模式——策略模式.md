#javaScript设计模式——策略模式

> 策略模式（Strategy）：将定义的一组算法封装起来，使其相互之间可以替换。封装的算法具有一定独立性，不会随客户端变化而变化。

##商品促销问题
> 问题描述：超时年底促销，部分商品5折销售，部分9折，普通用户满100返30，高级VIP用户满100返50...

对于前端，一般的处理方式可能是写多个方法，针对不同的优惠策略选择不同的方法来处理。如：

``` javascript
//100返30
function return30(price) {
    //dosomething
}
//100返50
function return50(price) {
    //dosomething
}
//9折
function percent90(price) {
    //dosomething
}
...
```

这里一个促销策略对应一个方法显得过于冗余，并且很不方便管理，代码阅读性较差。可能有的胖友会想到把这些都封装为一个方法，然后使用if或者switch语句来判断状态并返回策略，如：

``` javascript
function priceStrategy(algorithm,price) {
    switch (algorithm) {
        case 'return30':
            //dosomething
            break;
        case 'return50':
            //dosomething
            break;
        case 'percent90':
            //dosomething
            break;
    }
}
//调用方式
priceStrategy('return30',999);
```
这种方式较上一种已经好了很多，在策略的封装以及可读性提高了许多。但是这种方式在寻找策略的时候是从上到下一个一个的找，这样如果方法多了的话，多少还是会影响一些效率的，所以我们采用对象的方法，使用键值对，直接找到对应的策略方法：

```javascript
//价格策略对象
var priceStrategy = function() {
    var strategy {
        return30: function(price) {
            //do something
        },
        return50: function(price) {
            //do something
        },
        percent90: function(price) {
            //do something
        },
        percent50: function(price) {
            //do something
        }
    }
    //算法调用接口
    return function(algorithm, price) {
        //如果算法存在，则调用算法，否则返回false
        return strategy[algorithm] && strategy[algorithm](price);
    }
}();
```

总的来说，策略模式在平常的开发中还是比较常用的一个设计模式，希望对大家有帮助。