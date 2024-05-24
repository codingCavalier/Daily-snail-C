
### Apk打包过程

1.将aidl文件使用aidl工具转换为编译器能够识别编译的java文件<br>
2.将资源文件（AndroidManifest.xml，xml布局文件，资源resources文件，asserts下的资源文件）使用aapt工具（最近几个版本已经改为优化后的aapt2）一部分打包为编译后的资源文件（resources.arsc），并生成对应的R文件，以及生成Proguad 配置文件<br>
3.将java源文件以及aidl生成的java文件还有R文件，android类库文件使用javac一起编译为class文件<br>
4.使用proguard对class文件和资源文件进行混淆和优化。<br>
5.然后和第三方类库class文件一起打包生成Android虚拟机可以识别的Dalvik Byte Code类型的dex文件<br>
6.使用apkBuilder工具，将dex文件和资源文件以及so库打包生成未签名的apk文件<br>
7.使用jarSigner工具将apk文件使用keystore签名生成签名后的apk文件<br>
8.使用zipAlign对apk进行对齐处理，对齐的过程就是将 APK 文件中所有的资源文件距离文件的起始位置都偏移4字节的整数倍，这样通过 mmap 访问 APK 文件的速度会更快，并且会减少其在设备上运行时的内存占用<br>

### v1签名：签名三兄弟：1.MANIFEST.MF 2.CERT.SF 3.CERT.RSA

#### 1. MANIFEST.MF
该内容保存的是逐一遍历 APK 中的所有条目，使用摘要算法如SHA1或者SHA256算出摘要信息，并使用base64编码后，存放到MANIFEST.MF块中 每个块包含一个Name属性：存放该文件的路径

#### 2. CERT.SF
对MANIFEST.MF进行二次摘要

#### 3. CERT.RSA
使用私钥对CERT.SF进行签名，签名结果与公钥，证书一同写入此文件

#### 4. 将上述三个文件放入META-INF文件夹中

![image](https://github.com/codingCavalier/Daily-snail/assets/26496772/242bf85f-f970-472f-b1e0-c3cdd6c42488)

### v2签名：
v1缺点：<br>

签名速度慢，需要对每个文件进行签名<br>
完整性保持不够：因为META-INF目录用于签名，所以不会记录到校验信息内，用户可以在这个文件中随意添加文件<br>

v2签名：<br>
就是把 APK 按照 1M 大小分割，分别计算这些分段的摘要，最后把这些分段的摘要再进行计算得到最终的摘要也就是 APK 的摘要。然后将 APK 的摘要 + 数字证书 + 其他属性生成签名数据写入到 APK Signing Block 区块。<br>

v2 签名机制是在 Android 7.0 以及以上版本才支持。因此对于 Android 7.0 以及以上版本，在安装过程中，如果发现有 v2 签名块，则必须走 v2 签名机制，不能绕过。否则降级走 v1 签名机制。<br>
v1 和 v2 签名机制是可以同时存在的，其中对于 v1 和 v2 版本同时存在的时候，v1 版本的 META_INF 的 .SF 文件属性当中有一个 X-Android-APK-Signed 属性：X-Android-APK-Signed: 2

#### 对打渠道包的影响

之前的渠道包生成方案是通过在 META-INF 目录下添加空文件，用空文件的名称来作为渠道的唯一标识。但在新的应用签名方案下 META-INF 已经被列入了保护区了，向 META-INF 添加空文件的方案会对区块 1、3、4 都会有影响。

1.V2并行计算加快签名速度<br>
2.V2保证META-INF目录不会被篡改<br>

### apk安装过程

1.复制APK到/data/app目录下。解压并扫描包<br>
2.资源管理器解析apk里面的资源文件<br>
3.解析AndroidManifest文件，并在/data/data/目录下创建对应的应用数据目录<br>
4.然后对dex文件进行优化，并保存在dalvik-cache目录下。<br>
5.将AndroidManifest文件解析出的四大组件信息注册到PackageManagerService中<br>
6.安装完成后，发送广播。<br>


原文链接：https://juejin.cn/post/7136942559330467847
