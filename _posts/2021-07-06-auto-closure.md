---
layout: post
---

# 自动闭包
用于传递给函数作为参数的表达式，闭包不接受任何参数，当被调用时，会返回包装在其中的表达式的值
 
优点：
1. 省略闭包花括号，用普通表达式来替代显示闭包
2. 延时求值/执行


```swift
// 创建一个自动闭包
var customersInLine = ["chirs", "Alex", "Ewa", "Barry", "Daniella"]
print(customersInLine.count)

// 创建一个自动闭包：所谓自动闭包，就是调用时候才执行闭包内代码
let customerProviderClosure = {
    () -> String in
    return customersInLine.remove(at: 0)
}

// 优化自动闭包：()、返回值、in 均自动推到优化
let providerClosure = { customersInLine.remove(at: 0)}

// 自动闭包延时求值
print("Now serving \(providerClosure())!")
print(customersInLine.count)

// 闭包作为参数传递给函数，该闭包为显式闭包
func serve(customer customerProvider: () -> String) {
    print("Now serving \(customerProvider())!")
}

// 将入参通过 @autoclosure 转化为自动闭包
func serveAuto(customer customerProvider: @autoclosure () -> String) {
    print("now serving \(customerProvider())")
}

serve(customer: {customersInLine.remove(at: 0)}) // 显式闭包
serveAuto(customer: customersInLine.remove(at: 0)) // 可以增加代码复杂度呢，😀

// 自动逃逸闭包
func autoEscapingClosure(_ customerProvider: @autoclosure @escaping () -> String) {
    completionHandlers.append(customerProvider) // 捕获
}
autoEscapingClosure(customersInLine.remove(at: 0)) // 调用捕获逃逸闭包

for cusPro in completionHandlers {
    print("now get \(cusPro())") // 调用捕获的自动逃逸闭包
}

```