---
layout: post
---

# swift5.5 并发

> 使用Playground实践swift5.5 concurrency 章节

```
import Cocoa
import Foundation
import Darwin


/*
 并发 concurrency

 'async(priority:operation:)' is deprecated: `async` was replaced by `Task.init` and will be removed shortly.
 async -> Task.init

 'get()' is deprecated: get() has been replaced by .value
 get() -> value

 */

// 模拟异步下载图片
func downloadPhoto(named: String) async -> Any {
    await Task.sleep(1 * 1_000_000_000)
    return "图片 => " + named
}

func show(photo: Any) {
    print(photo)
}


// 定义和调用异步函数
func listPhotos(inGallery name: String) async -> [String] {
    await Task.sleep(2 * 1_000_000_000) // 2 seconds
    let resutlt = ["some asynchronous networking code ...", "mock 1", "mock 2", "mock 3"]
    return resutlt
}

// 调用异步函数
async {
    let photoNames = await listPhotos(inGallery: "Summer Vacation") // 调用异步函数
    let sorteNames = photoNames.sorted()
    let name = sorteNames[0]
    let photo = await downloadPhoto(named: name)
    show(photo: photo)
}


// 异步序列迭代
let handle = FileHandle.standardInput
async { // avoid async call in a function does not support concurrency
    for try await line in handle.bytes.lines {
        print(line)
    }
}

// 并行调用异步函数
async {
    // 串行异步
    let photoNames = await listPhotos(inGallery: "Summer Vacation") // 调用异步函数
    let firstPhoto = await downloadPhoto(named: photoNames[0])
    let secondPhoto = await downloadPhoto(named: photoNames[1])
    let photos = [firstPhoto, secondPhoto]
    show(photo: photos)

    // 并行异步
    async let firstPhotoC = downloadPhoto(named: photoNames[0])
    async let secondPhotoC = downloadPhoto(named: photoNames[1])
    let photosC = await [firstPhotoC, secondPhotoC]
    show(photo: photosC)
}


// 任务和任务组：结构化并发，父子任务
async {
    await withTaskGroup(of: String.self, body: { taskGroup in //Data.self -> 类比oc [xxx class]
        let photoNames = await listPhotos(inGallery: "Summer Vacation")
        for name in photoNames {
            taskGroup.async(priority: .default, operation: { // 可以根据任务指定优先级
                await downloadPhoto(named: name) as! String
            })
        }
    })
}

// 非结构化并发
Task.init(priority: .default) {
    let handle = Task.init(priority: .default) {
        return await listPhotos(inGallery: "Summer Vacation")
    }
    let result = await handle.value
    show(photo: result)

    // 下一个小章节：任务取消
    if handle.isCancelled {
        handle.cancel() // 手动取消任务
    }
}


// 任务取消👆[看上面]


/*
 Actor 引用类型
 data reaces avoid
 let is safe for concurrency class


 */

// actor 创建的结构体【一次只允许一个task访问其可变状态】
actor TemperatureLogger {
    let label: String
    var measurements: [Int]
    private(set) var max: Int // 可能暂停的点

    init(label: String, measurement: Int) {
        self.label = label
        self.measurements = [measurement]
        self.max = measurement
    }
}

// 扩展
extension TemperatureLogger {
    func update(with measurement: Int) { // 不包含终端，会访问错误
        measurements.append(measurement)
        if measurement > max {
            max = measurement
        }
    }
}

let logger = TemperatureLogger(label: "Outdoors", measurement: 25)
Task.init(priority: .default, operation: {
    // 参与者隔离 actir isolation
    print(await logger.max)
//    print(logger.max) // Expression is 'async' but is not marked with 'await'
})
```