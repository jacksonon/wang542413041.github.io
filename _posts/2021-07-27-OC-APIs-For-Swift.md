---
layout: post
---

#  为Swift改进Objective-C接口适用性

## 重命名

### 重命名类的名称

标记@interface，且重命名的名称点符号不可以包含'.'符号(官方文档有，但是报错)；
```
NS_SWIFT_NAME(MydefineUseful)
@interface TSMydefineUseful : NSObject
```

### 重命名属性的名称

```
@property (nonatomic, assign) BOOL canSay NS_SWIFT_NAME(isSay);
```

### 重命名方法的名称


```
- (void)sayByeToA:(id)a toB:(id)b NS_SWIFT_NAME(sayBye(toA:toB:)); // 实例方法重命名
+ (instancetype)sharedInstance NS_SWIFT_NAME(shared()); // 工厂方法重命名

```

### 重命名枚举的名称

```
typedef NS_ENUM(NSUInteger, MydefineUsefulInfoType) {
    myname NS_SWIFT_NAME(name),
    myage NS_SWIFT_NAME(age),
    mylike NS_SWIFT_NAME(like),
} NS_SWIFT_NAME(MydefineUseful.InfoType);
```

## 改进声明

### 改进方法的声明

在 Swift 中引用 Objective-C 单例时，如果单例方法包含于类名，则会出现编译错误，准确的说，应该是如果单例方法的名称正好是该类名驼峰命名的后缀，那么在 Swift 中引用该单例方法时，会出现编译错误。

The Swift compiler automatically imports Objective-C code as conventional Swift code. It imports Objective-C class factory methods as Swift initializers, and Objective-C enumeration cases truncated names.
There may be edge cases in your code that are not automatically handled. If you need to change the name imported by Swift of an Objective-C method, enumeration case, or option set value, you can use the NS_SWIFT_NAME macro to customize how a declaration is imported.

Swift编译器自动导入Objective-C代码作为常规Swift代码。它将Objective-C类工厂方法作为Swift初始值设定项导入，并将Objective-C枚举实例作为截断名称导入。
因为 Swift 编译器在使用 Objective-C 的代码时会自动的将其转成 Swift 风格的代码，就是会对一些方法名、枚举名等等做一些有规则的删减。


```
+ (instancetype)sharedInstance NS_REFINED_FOR_SWIFT;
- (void)alwaysWantSayHelloToEveryone:(id)everyone NS_REFINED_FOR_SWIFT; // 公开现有的OC-API
```

```
extension MydefineUseful {
    
    // 单例的计算属性
    var shared: MydefineUseful {
        get {
            MydefineUseful.__sharedInstance()
        }
    }
    
    // 实现新的API
    func pleaseSayHello(toEvery: String) {
        // 重用现有的方法，保持相同的功能
        return MydefineUseful().__alwaysWantSayHello(toEveryone: toEvery)
    }
}
```

## 禁用

### 标记在Swift中禁用

```
- (void)canCallOnlyObjective NS_SWIFT_UNAVAILABLE("Don't call it in Swift"); // swift中不可用
```


