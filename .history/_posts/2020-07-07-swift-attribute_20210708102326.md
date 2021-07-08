#  swift 属性

### available
1. 版本标记`@available(iOS 10.0, macos 10.12, *)`
2. 语言标记`@available(swift 5.0)`
3. API标记`@available(*, unavailable, renamed: "NewName")`

### discardableResult

给一个方法或方法声明使用，支持取消未使用返回值的编译错误，如
```Swift
@discardableResult
func someFunc() -> int { return 10 }
someFunc() // 编译错误，未使用返回值不会提示，因为使用了忽略
```

### dynamicCallable

动态可调用类型，类比于接收任意数量参数的函数一样。需要实现`dynamicallyCall(withArguments:或withKeywordArguments)`中的最少一个
```swift
@dynamicCallable
struct DynamicStruct {
    func dynamicallyCall(withArguments someParam: [Any]) {
        // do some
    }
}

// can call as
let dial = DynamicStruct()
dial(4, 1, 1)
dial.dynamicallyCall(withArguments: [4, 1, 1])
```

### dynamicMemberLookup

将此属性应用于类、结构、枚举或协议，以允许在运行时按名称查找成员.该类型必须实现一个`subscript(dynamicMember:)`方法

可以接收`KeyPathWritableKeyPath`或`ReferenceWritableKeyPath`类型
1. 可用于为无法在编译时进行类型检查的数据创建包装器类型
    ```swift
    dynamicMemberLookup
    struct DynamicStruct {
        let dictionary = ["someDynamicMember": 325,
                          "someOtherMember": 787]
        subscript(dynamicMember member: String) -> Int {
            return dictionary[member] ?? 1054
        }
    }
    let s = DynamicStruct()

    // Use dynamic member lookup.
    let dynamic = s.someDynamicMember
    print(dynamic)
    // Prints "325"

    // Call the underlying subscript directly.
    let equivalent = s[dynamicMember: "someDynamicMember"]
    print(dynamic == equivalent)
    // Prints "true"
    
    ```
2. 键路径的动态成员查找可用于以支持编译时类型检查的方式实现包装器类型
    ```
    struct Point { var x, y: Int }

    @dynamicMemberLookup
    struct PassthroughWrapper<Value> { // 泛型Value↩️
        var value: Value
        subscript<T>(dynamicMember member: KeyPath<Value, T>) -> T { // 遵循KeyPathWritable类型
            get { return value[keyPath: member] } // 使用member进行查找
        }
    }

    let point = Point(x: 381, y: 431)
    let wrapper = PassthroughWrapper(value: point)
    print(wrapper.x)
    ```

### frozen

将此属性应用于结构或枚举声明以限制您可以对类型进行的更改种类。


### inlinable

该特性用于函数、方法、计算属性、下标、便利构造器或析构器的声明，以将该声明的实现公开为模块公开接口的一部分。编译器允许在调用处把 inlinable 标记的符号替换为符号实现的副本。

内联代码可以与任意模块中 public 访问级别的符号进行交互，同时可以与在相同模块中标记 usableFromInline 特性的 internal 访问级别的符号进行交互。内联代码不能与 private 或 fileprivate 级别的符号进行交互。

该特性不能用于嵌套在函数内的声明，也不能用于 fileprivate 或 private 访问级别的声明。在内联函数内定义的函数和闭包都是隐式内联的，即使他们不能标记该特性。

### main
标记程序入口

### nonobjc
针对方法、属性、下标、或构造器的声明使用该特性将覆盖隐式的 objc 特性。nonobjc 特性告诉编译器该声明不能在 Objective-C 代码中使用，即便它能在 Objective-C 中表示。

### NSApplicationMain

在类上使用该特性表示该类是应用程序委托类。使用该特性与调用 NSApplicationMain(_:_:) 函数的效果相同。
```
import AppKit
NSApplicationMain(CommandLine.argc, CommandLine.unsafeArgv)
```

### NSCopying
该特性用于修饰一个类的存储型变量属性

### NSManged
该特性用于修饰 NSManagedObject 子类中的实例方法或存储型变量属性，表明它们的实现由 Core Data 在运行时基于相关实体描述动态提供。对于标记了 NSManaged 特性的属性，Core Data 也会在运行时为其提供存储。应用这个特性也意味着 objc 特性。

### objc
该特性用于修饰任何可以在 Objective-C 中表示的声明。

在以下情况中同样会隐式的添加 objc 特性：
- 父类有 objc 特性，且重写为子类的声明。
- 遵循带有 objc 特性协议的声明。
- 带有 IBAction、IBSegueAction、IBOutlet、IBDesignable、IBInspectable、NSManaged 或 GKInspectable 特性的声明。


### objcMembers
该特性用于类声明，以将 objc 特性应用于该类、扩展、子类以及子类的扩展的所有 Objective-C 兼容成员。

### propertyWrapper
在类、结构体或者枚举的声明时使用该特性，可以让其成为一个属性包装器。
