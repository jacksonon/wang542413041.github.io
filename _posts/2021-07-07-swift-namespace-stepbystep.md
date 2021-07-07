#  swift 命名空间


> 循序渐进获取一个优雅的swift命名空间

### 实现一个命名空间
1. 创建结构体，并实现指定方法
2. 扩展指定类型的控件
3. 调用指定类的命名空间属性持有实例的方法

```
struct ButtonNameSpace {
    func hello()  {
        print("hello bae namespace")
    }
}

extension UIButton {
    var namespace: ButtonNameSpace {
        return ButtonNameSpace()
    }
}

UIButton().namespace.hello()
```



###创建一个持有该类的命名空间
1. 创建结构体，结构体持有指定类型；包含初始化方法；包含自定义方法
2. 扩展指定类，添加命名空间属性，将self传入该结构体
3. 调用类.命名空间.指定方法(可以访问指定类属性)
 
 ```
 struct ButtonNameSpace {
     let btn: UIButton
     init(_ btn: UIButton) {
         self.btn = btn
     }
     func hello() {
         print("hello \(self.btn.titleLabel?.text ?? "")")
     }
 }

 extension UIButton {
     var namespace: ButtonNameSpace {
         return ButtonNameSpace(self)
     }
 }

 let btn = UIButton()
 btn.titleLabel?.text = "hehehe"
 btn.namespace.hello()
``` 



### 使用泛型命名空间
1. 创建泛型结构体
2. 扩展指定类持有命名空间属性，该属性持有结构体
3. 通过where语句为特定类扩展方法
4. 通过命名空间调用方法
 ```
 struct MyNameSpace<Base> {
     let base: Base
     init(base: Base) {
         self.base = base
     }
 }

 extension UIButton {
     var namespace: MyNameSpace<UIButton> {
         return MyNameSpace(base: self)
     }
 }

 extension MyNameSpace where Base: UIButton { // Base == UIButton
     func hello() {
         print("hi \(base.titleLabel?.text ?? "")")
     }
 }


 let btn = UIButton()
 btn.titleLabel?.text = "WANG"
 btn.namespace.hello()
```



### 使用协议和泛型扩展实现通用命名空间
1. 创建一个传入泛型数据的结构体
2. 创建一个持有泛型占位符的协议，并创建命名空间属性，和命名空间类型属性
3. 扩展协议，实现属性、类型属性的get方法
4. 扩展步骤一创建的结构体，可以使用`where`给指定的类绑定
5. 指定类继承步骤二创建的协议，获取扩展结构体扩展的方法
6. 创建实例并调用查看结果

```
public struct NameSpace<Base> {
    
    /// Base object  to extend
    public let base: Base
    
    /// Create extensions with base object
    /// - Parameter base: Base object
    public init(_ base: Base) {
        self.base = base
    }
}

public protocol NameSpaceCompatible {
    /// extended type
    associatedtype CompatibleType
    
    /// Perfect World Extension
    static var wm: NameSpace<CompatibleType>.Type { get set }
    
    /// Perfect World extension
    var wm: NameSpace<CompatibleType> { get set }
//    var wm_Self: NameSpace<Self> { get set }
//    static wm_Self: NameSpace<Self>.Type { get set }
}

extension NameSpaceCompatible {
    public static var wm: NameSpace<Self>.Type {
        get {
            return NameSpace<Self>.self
        }
        set {
            
        }
    }
    
    public var wm: NameSpace<Self> {
        get {
            return NameSpace(self)
        }
        set {
            
        }
    }
}


// 给特定的类加上命名空间

class Custom {
    init(name: String) {
        self.name = name
    }
    var name: String
    func myfunc() {
        print("just use namespace run my func")
    }
}
// 给所继承的基类添加方法
extension NameSpace {
    func baseHello() {
        print("hi type is \(base.self) and name is \((base as! Custom).name)")
    }
}

extension Custom: NameSpaceCompatible {}

let cm = Custom(name: "WANG")
cm.myfunc()
cm.wm.baseHello()
```
