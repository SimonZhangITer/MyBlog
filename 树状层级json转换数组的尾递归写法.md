> 树状层级包含关系的JSON转换为一个数组列表，使用尾递归算法不仅代码简单，而且性能高效，不会爆栈


> 需求简介： 把树状的JSON数据连起来转换成一个个的数组，如：item1层有一个item2，它的下面有两个item2\_1，item2\_2，那么转换为数据就是 [{item1:"item1\_key",item2\_1:"item2\_1\_key"},{item1:"item1\_key",item2\_2:"item2\_2\_key"}]，也就是循环遍历

## 思路

_优雅的写代码_

可以看到，我们是从JSON里面取数据，那么对于Object类型，一般都是使用item.key这样的方式去取数据，但是对于现在的这个需求，这样写显然是很不优雅的一种写法，而且通用性很低，字段名字一改就都得改了。

所以：我们把需要取的字段名按顺序放在一个数据里面，记录index，每次从数组中取字段名称，这样代码看起来既简洁，维护起来也很方便。

## 对象闭包问题解决

这里插一句关于对象的闭包问题：for循环当中，我们取了当前的i值，肯定会发生闭包，那么简单的解决办法就是：

> 先将对象转成字符串，再转成对象，这样来回一转就取到的是对象值了
> 
> var str = JSON.stringfy(obj)
> 
> JSON.parse(str)



代码如下，大家可以放到网页中看一下控制台输出：

```javascript
var json = {
    item1: {
        buckets: [
            {
                key: 'item1_1',
                item2: {
                    buckets: [
                        {
                            key: 'item2_1',
                            item3: {
                                buckets: [
                                    {
                                        key: 'item3_1',
                                        name: 'name'
                                    }, {
                                        key: 'item3_2',
                                        name: ''
                                    }
                                ]
                            }
                        }, {
                            key: 'item2_2',
                            item3: {
                                buckets: [
                                    {
                                        key: 'item3_1',
                                        name: 'name'
                                    }
                                ]
                            }
                        }, {
                            key: 'item2_3',
                            item3: {
                                buckets: [
                                    {
                                        key: 'item3_1',
                                        name: 'name'
                                    }
                                ]
                            }
                        }
                    ]
                }
            }, {
                key: 'item1_2',
                item2: {
                    buckets: [
                        {
                            key: 'item2_1',
                            item3: {
                                buckets: [
                                    {
                                        key: 'item3_1',
                                        name: 'name'
                                    }
                                ]
                            }
                        }, {
                            key: 'item2_2',
                            item3: {
                                buckets: [
                                    {
                                        key: 'item3_1',
                                        name: 'name'
                                    }
                                ]
                            }
                        }
                    ]
                }
            }
        ]
    }
}
var filedArr = ['item1', 'item2', 'item3', 'name']
var arr = []
function recursion(obj, tempObj, index) {
    var key = filedArr[index];
    for (var i = 0; i < obj[key].buckets.length; i++) {
        tempObj[key] = obj[key].buckets[i].key
        if (index == filedArr.length - 2) {
            var temp = JSON.stringify(tempObj)
            arr.push(JSON.parse(temp))
        } else {
            recursion(obj[key].buckets[i], tempObj, index + 1)
        }
    }
}
recursion(json, {}, 0);
console.log(arr);
```