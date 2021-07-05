---
layout: post
---

# 尾随闭包

场景：
1. 自定义函数，函数传递的参数包含闭包
2. 调用函数，可以按照正常的调用，将闭包写在函数内
3. 如闭包过大，可以优化为尾随闭包，从而进一步优化代码

尾随闭包优化：
1. 尾随闭包可以放在方法func(){}后
2. 在使用尾随闭包的时候，如闭包为唯一参数可以把func()的()省略
3. 适合多行业务逻辑的闭包
 

```swift
var reversedNames1 = names.sorted(by: {$0 > $1})
var reversedNames2 = names.sorted() {$0 > $1}
var reversedNames3 = names.sorted { $0 > $1 } // 尾随闭包，不能使用运算符优化
```
