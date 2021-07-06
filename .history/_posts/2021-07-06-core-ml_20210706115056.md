# CoreML 概览


> 在App中整合机器学习模型，客户端级别的机器学习

Core ML 是基于Accelerate和BNNS（用于构建和运行用于训练和推理的神经网络的子程序）提供大规模高性能且低功耗的数学和图像计算，Metal Performance Shaders GPU高度优化的计算和图形着色器

### 从一个简单的例子看图像识别coreML整体流程：
1. 添加图像识别 xx.mlmodel文件
2. 在xcode中点击该文件，进入 preview模块 查看并实践功能
3. 创建图像输入源，及图形转化代理
4. 使用VNCoreMLModel根据路径创建识别模型，构建VNCoreMLRequest进行识别
5. 使用VNImageRequestHandler创建图片分析请求，调用perform进行图像识别
6. 根据结果进行相应处理或反馈


### Core ML 机器学习分类

> 此处特指在iOS设备端所涉及的机器学习类型

1. 计算机视觉`Vision`
2. 自然语言`Natural Language`
3. 语音识别`Speech`
4. 声音分析`Sound Analysis`


### 获取Core ML 模型(.mlmodel文件)
1. Apple已训练好的[热门开源模型](https://developer.apple.com/cn/machine-learning/models/)
2. 其它平台训练好的模型使用[Core ML Tools](https://github.com/apple/coremltools/blob/main/README.md)进行转化
3. 使用[Create ML 应用](https://developer.apple.com/cn/documentation/createml/)构建和训练模型


### 训练模型

> 包含：图像训练、文本训练、表格训练、声音、视频等

图像分类
1. 单张图片 -> Image classifier -> 类别标签
2. 已标记图像示例 -> Create ML -> 图像分类器结果
3. 训练完成后，根据评估的数据进行Core ML模型文件导出，在您的应用程序中使用

文本分类（训练文本分类器的方法是向它展示大量已标记的文本示例）
1. 收集文本数据并导入到 MLDataTable 实例中。您可以从 JSON 和 CSV 格式创建数据表
2. 从大量的数据中分割为训练用、评估用, `用 MLDataTable (英文) 的 randomSplit(by:seed:)`将数据分配到不同数据存储中`let (trainingData, testingData) = data.randomSplit(by: 0.8, seed: 5)`
3. 创建和训练文本分类器`MLTextClassifier`
4. 使用分离的测试数据评估训练准确度`let evaluationMetrics = sentimentClassifier.evaluation(on: testingData)`获取运行结果后`let evaluationAccuracy = (1.0 - evaluationMetrics.classificationError) * 100`计算准确性
5. 存储coreML模型，使用`MLModelMetadata`创建带有描述性的实例后写入磁盘指定位置即可
6. 将模型添加到App中并使用


### 缩减 Core ML App 的大小
1. 转化为较低精度模型，基本所有模型均有浮点权重，权重越大对应的mlmodel文件越大，使用低精度可以大幅度减小包体积
2. 运行时下载和编译模型，预下载包不包含mlmodel文件，使用其它网络方式下载文件，持久化保存在应用某处，使用`try MLModel.compileModel(at: modelDescriptionURL)`进行模型加载



### 关联文章
[训练神经网络识别数字](https://developer.apple.com/documentation/accelerate/training_a_neural_network_to_recognize_digits)
[创建并训练一个图像识别模型](https://developer.apple.com/documentation/createml/creating_an_image_classifier_model)
