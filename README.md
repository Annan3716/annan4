# 实验4_实现智能图像分类APP  

## 一、实验内容  
按照教程构建基于**TensorFlow Lite**的Android图像分类应用，重点关注：  
- **CameraX库（`androidx.camera.*`）** 的使用；  
- **数据视图模型（ViewModel）** 的使用。  
上传代码至GitHub并撰写README文档。  


## 二、实验步骤  


### 下载代码编译运行  
1. 从https://github.com/hoitab/TFLClassify下载项目文件，解压至Android Studio，按教程编译并解决错误。  
   ![image-20250615140011](EX4_img/download.png)  


### 向应用中添加TensorFlow Lite模型  
1. 右键“start”模块，选择**File > New > Other > TensorFlow Lite Model**。  
   ![image-20250615140233](EX4_img/import1.png)  
2. 选择自定义模型（如finish模块中ml文件夹下的`FlowerModel.tflite`）。  
   ![image-20250615140345](EX4_img/import2.png)  
3. 导入成功后，模型依赖自动添加至`build.gradle`，生成摘要信息。  
   ![image-20250615140502](EX4_img/model_summary.png)  


### 检查代码中的TODO项  
1. 通过**View > Tool Windows > TODO**查看未完成项。  
   ![image-20250615140622](EX4_img/todo1.png)  
   ![image-20250615140711](EX4_img/todo2.png)  


### 添加代码重新运行APP  
1. **MainActivity.kt中TODO 1**：初始化模型  
   ```kotlin
   // 加载模型  
   val model = Interpreter(File(assets, "FlowerModel.tflite"))  
   // 配置模型选项  
   val options = Interpreter.Options().apply { setNumThreads(4) }  
   // 初始化分类器  
   classifier = TensorFlowLiteClassifier(model, options, labelList)
   ```  
   ![image-20250615141033](EX4_img/code1.png)  
2. **CameraX的analyze方法中**：转换图像格式  
   ```kotlin
   // ImageProxy转Bitmap  
   val bitmap = imageProxy.toBitmap()  
   // Bitmap转TensorImage  
   val tensorImage = TensorImage.fromBitmap(bitmap)
   ```  
   ![image-20250615141215](EX4_img/code2.png)  
3. **处理识别结果**：按概率排序并筛选前`MAX_RESULT_DISPLAY`项  
   ```kotlin
   val results = classifier.recognizeImage(tensorImage)  
   // 降序排序  
   val sortedResults = results.sortedByDescending { it.score }  
   // 取前k个结果  
   val topResults = sortedResults.take(MAX_RESULT_DISPLAY)
   ```  
   ![image-20250615141402](EX4_img/code3.png)  
4. **构建Recognition数据**：存入label和score  
   ```kotlin
   topResults.forEach {  
       val recognition = Recognition(it.id, it.title, it.score)  
       recognitionList.add(recognition)  
   }
   ```  
   ![image-20250615141533](EX4_img/code4.png)  
5. **删除虚拟结果代码**：注释原模拟数据生成逻辑。  
   ![image-20250615141644](EX4_img/remove_code.png)  
6. **物理设备运行**，最终效果：  
   ![image-20250615141801](EX4_img/result.png)  


### 上传代码与README撰写  
1. 代码上传至GitHub，目录结构包含：  
   - app/src/main/java/... （主代码）  
   - app/src/main/assets/ （模型文件）  
   - app/build.gradle （依赖配置）  
2. README内容：  
   - 项目功能：基于TensorFlow Lite的花卉实时识别  
   - 技术点：CameraX相机调用、ViewModel数据管理、模型推理流程  
   - 运行步骤：环境要求、模型导入方式、编译说明。
