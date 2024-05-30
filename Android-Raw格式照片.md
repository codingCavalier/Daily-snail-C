### 方案一

替换CameraX的ImageCapture，改为传入bufferFormat为ImageFormat.RAW_SENSOR的ImageCapture

原理

CameraX本身是支持拍摄Raw格式的照片的，只不过拍摄照片的默认UseCase（即ImageCapture）默认拍摄JPEG格式照片，我们可以自己组装一个ImageCapture，或者自己实现一个UseCase去拍摄Raw，这里没必要那么麻烦，直接用已有的实现，替换一下拍摄格式就好了。

优缺点

优点：<br>
1、实现方式简单<br>
2、侵入性小，最大限度保持CameraX原有逻辑<br>
3、稳定且兼容性好，归功于CameraX的高兼容性<br>
缺点：<br>
1、有些手机本身支持Raw（系统api说支持），但是被CameraX以兼容性或者其他原因拦截，而不能拍摄Raw。（可能也不是缺点，因为有些手机强行拍Raw以后，拍出来是坏的，画面色调完全不对，详见方案二）<br>

实现

1、创建ImageCapture对象：使用Builder，修改ImageCaptureConfig里的OPTION_BUFFER_FORMAT为ImageFormat.RAW_SENSOR <br>
2、一些参数的保留，比如旋转角度、IOExecutor，同时还需要设置targetResolution <br>
3、利用反射做替换，替换时机就是进入Raw模式的时候做替换，退出Raw模式的时候记得替换回JPEG格式的ImageCapture <br>

### 方案二

替换CameraX的ImageCapture里的ImagePipeline，改为拍摄Raw格式的ImagePipeline

原理

此方案是为了解决方案一的缺点问题，而进行的探索。方案一的缺点，导致有些手机支持Raw，但是被CameraX拦截（可能没反应，可能崩溃），导致拍不了Raw。采用方案二，可以绕过CameraX的拦截，强行拍Raw。<br>
原理是预览时，采用默认Preview UseCase和JPEG格式的ImageCapture配合，欺骗CameraX，让它以为要拍摄JPEG格式的照片，从而实现正常预览。然后我们把刚刚JPEG格式的ImageCapture里的ImagePipeline替换掉，换成我们新创建的拍摄Raw格式的ImagePipeline，这样点击拍摄时，拍出来自然就是Raw格式照片。

优缺点

完全和方案一相反，这个方案只是解决了方案一的不足的一点，实现了只要手机系统api说支持Raw，那我们就拍，完全不管拍出来的结果如何，以及逻辑是否稳定，不建议采用。

实现

这里的替换不像方案一那么简单，因为ImagePipeline在ImageCapture里不是保持不变的，而是可能发生变化的，在添加UseCase、移除UseCase两种情况下，会产生新的ImagePipeline

1、创建ImageCaptureConfig对象：

```kotlin
val mutableConfig = ImageCapture.Builder.fromConfig(imageCaptureConfig)
                .setBufferFormat(ImageFormat.RAW_SENSOR)
                .mutableConfig
            mutableConfig.insertOption(ImageInputConfig.OPTION_INPUT_FORMAT, ImageFormat.RAW_SENSOR)
            val newImageCaptureConfig = ImageCaptureConfig(OptionsBundle.from(mutableConfig
```

2、复刻ImageCapture里的createPipeline方法，简单修改一下，没用的删掉：

```kotlin
private fun createPipeline(
        imageCapture: ImageCapture,
        imageCaptureControl: ImageCaptureControl,
        oldTakePictureManager: TakePictureManager?,
        oldImagePipeline: ImagePipeline?,
        oldSessionConfigBuilder: SessionConfig.Builder?,
        config: ImageCaptureConfig,
        streamSpec: StreamSpec
    ): Pair<TakePictureManager, ImagePipeline> {
        val resolution = streamSpec.resolution

        val isVirtualCamera = false

        oldImagePipeline?.close()

        val imagePipeline = ImagePipeline(config, resolution, imageCapture.effect, isVirtualCamera)

        val takePictureManager = oldTakePictureManager ?: TakePictureManager(imageCaptureControl)
        takePictureManager.imagePipeline = imagePipeline

        val sessionConfigBuilder = imagePipeline.createSessionConfigBuilder(streamSpec.resolution)
        val newSessionConfig = sessionConfigBuilder.build()

        // 移除老的surface
        oldSessionConfigBuilder?.clearSurfaces()

        // 加入新的surface
        newSessionConfig.surfaces.firstOrNull()?.let {
            oldSessionConfigBuilder?.addNonRepeatingSurface(it)
        }

        return takePictureManager to imagePipeline
    }
```
    
3、调用createPipeline方法，产生新的TakePictureManager和ImagePipeline <br>
4、反射替换ImageCapture里的TakePictureManager <br>
5、反射替换ImageCapture里的ImagePipeline <br>
6、更新ImageCapture里的SessionConfig，这样才能把新产生的Surface安排上，让它可以在后续流程里被注册并接收画面数据 <br>
UseCaseReflecter.updateSessionConfig(imageCapture, oldSessionConfigBuilder.build()) <br>
7、触发CaptureSession重建，进而把刚刚产生的Surface注册进去。<br>
UseCaseReflecter.notifyReset(imageCapture) <br>

### 方案三

直接使用Camera2拍摄

原理

用最原始的方式打开Camera2，是相当酸爽的。我也只是搞了一些初级开发，像拍摄时候的预捕捉，对焦区域的转换计算，闪光灯，曝光等参数的配合设置等，不是简单写写调用即可。这一块，CameraX里其实有实现逻辑，可以看一下学习学习。

优缺点

这不是一般程序员该考虑的打开方式，除非专业做相机这一块的开发。考虑到兼容性，稳定性，快速迭代等，还是用CameraX比较合适。

### 一些杂七杂八的记录

#### 1、拍摄格式是如何传入ImagePipeline的

CaptureNode.In 会读取 ImageCaptureConfig 的 inputFormat

ImagePipeline：<br>
1、CaptureNode.In ->输入-> CaptureNode ->输出-> CaptureNode.Out <br>
2、CaptureNode.Out ->输入-> SingleBundlingNode ->输出-> ProcessingNode.In <br>
3、ProcessingNode.In ->输入-> ProcessingNode ->输出-> 照片<br>

创建ImageCapture(修改ImageCaptureConfig)：<br>
1、继承关系：ImageCaptureConfig : UseCaseConfig : ImageInputConfig(里面有OPTION_INPUT_FORMAT)<br>
2、修改 OPTION_BUFFER_FORMAT 就会内部修改 OPTION_INPUT_FORMAT <br>
3、OPTION_INPUT_FORMAT 就是 CaptureNode.In 读取的 inputFormat <br>

#### 2、CaptureNode.transform(CaptureNode.In) 时，会创建对应 format 的 ImageReader

有 metadata（真实相机），且我们通过 ImageCaptureConfig 不提供 ImageReaderProxyProvider，那么使用 MetadataImageReader；<br>
否则，要么使用我们产生的 ImageReaderProxy（我们提供了ImageReaderProxyProvider），要么使用 AndroidImageReaderProxy。<br>

#### 3、目前CameraX如果拍摄Raw格式的照片，不能直接落地，会抛异常，只能拍到内存中，然后通过DngCreator读成流，然后再考虑是写文件还是做其他处理
![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/480eddf5-2f61-4086-968a-b2b1cc5dbac0)

