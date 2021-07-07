#  CocoaPods 编译问题

支持@import导入
1. podfile中对target下的某个依赖指定`:modular_headers => true`
2. podfile中对target整体使用`use_modular_header!`，(这里的!是必须的意思)，可以使用`:modular_headers => false`对部分进行排除


指定使用library库还是framework框架
> 框架 = 库（静态库/动态库）+ .h（头文件） + bundle（资源包）
> .tbd 文件本质上是一个 YAML 文本文件，其描述了需要链接的动态库信息，其主要目的是 减小应用程序的下载大小
> 对于 OSX/iOS，静态库的后缀是 .a，动态库的后缀是 .dylib；在 UNIX 中，静态库的后缀是 .a，动态库的后缀是 .so
> 在 OSX/iOS 中，库采用 Mach-O 格式进行存储；在 UNIX 中，库采用 ELF 格式进行存储。
1. `use_frameworks!`强制使用框架而不是库的形式
2. 可以再其后主动使用`:link: => :dynamic`或static形式


