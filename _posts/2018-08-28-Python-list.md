---
layout: post
title:  "Python的list和dict嵌套用法"
date:   2018-08-28 17:31:00
categories: python
tags: python
excerpt: python的list、dict
mathjax: true
---
python中的list和dict是经常会用到的。这里把list和dict嵌套的一些用法写出来，供大家参考。   


先写一下list的切片:   
```
list = [1,2,3,4,5]
```    
list[0]表示list中0位置的值，这里的返回自然是1
注意list是以0开始计位置的。   

其他的list切片命令及说明：
```
list[2] #取第三位的字符
list[-2] #取倒数第二个字符
list[0:2] #取第一位到第三位的字符
list[0:-2] #取倒数第二个字符到开头的
list[:-2] #跟上一行的用法是一个意思
list[::-1] #按反向顺序重新取整个list
list[:] #取字符串的全部字符
list[1:] #取第二个字符到结尾
list[-3:-1] #取倒数第三位与倒数第一位之间的
list[-2:] #取倒数第二位到list末尾
list[:5:2] #从开头到第六位，间隔2个的字符，取出
list[:-5:-2] # 倒着，从末尾到倒数第五位， 间隔2个的字符，取出  
```
如果我们要给list中的某个位置赋值：
```
list[0]='a'
list
['a',2,3,4,5]
```
给list追加一条记录
```
list.append(6)
```
再检查list就可以看到多了一个字符6 
```
['a',2,3,4,5,6]
```  

以上list中的数据项只是字符串，如果数据项是dict呢？   
还是举例说明：
```
a = {"a1":1, "a2":2, "a3":3}
b = {"b1":1, "b2":2, "b3":3}
c = {"c1":1, "c2":2, "c3":3}
#初始化三个dict
list = [a, b, c]
list
```
最后生成list就是包含dict的list，返回结果如下：
```
[{'a1': 1, 'a2': 2, 'a3': 3},
 {'b1': 1, 'b2': 2, 'b3': 3},
 {'c1': 1, 'c2': 2, 'c3': 3}]
```

我们取这个list中的某个key的value，可以这样操作：  
```
list[0]["a1"]
```
  
下面是简单的一个例子。  
定义了一个方法，从list1中取值，并做为value赋值到字典a中，并生成一个新的list：
```
def test():
    list1 = ['192.168.1.1', '192.168.1.2', '192.168.1.3', '192.168.1.4']
    a = {}
    list2 = []
    while len(list2) < len(list1):
        for num in list1:
            a = {'IPaddr': num}
            list2.append(a)
    return list2
```
调用上面的方法，返回结果：
```
[{'IPaddr': '192.168.1.1'}, {'IPaddr': '192.168.1.2'}, {'IPaddr': '192.168.1.3'}, {'IPaddr': '192.168.1.4'}]
```

dict中包含list：
```
    dict1 = {
        "server":
            [
                {"hostname": "", "username": "", "password": ""},
            ]
    }
```
以下代码通过循环向字典中的相关参数赋值：
```
dict1 = {
    "server":
        [
             {"hostname": "", "username": ""},
        ]
}
list = ['192.168.1.1', '192.168.1.2', '192.168.1.3', '192.168.1.4']
del dict1["server"][0]
for num in list:
    for k, v in dict1.items():
        v.append({"hostname": num})
print(dict1)
```
返回结果：
```
{'server': [{'hostname': '192.168.1.1'}, {'hostname': '192.168.1.2'}, {'hostname': '192.168.1.3'}, {'hostname': '192.168.1.4'}]}
```

list和dict的嵌套使用其实就是通过for循环遍历再append到dict的相关参数中，多加练习便可掌握。