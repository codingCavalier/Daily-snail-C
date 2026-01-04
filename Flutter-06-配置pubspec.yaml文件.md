#### name
- 项目的唯一标识，发布到maven时作为项目名称，其他项目引用时作为包名：import 'package:这里是name的值/my_widget.dart';

#### publish_to
- 发布到maven仓库的配置
- 发布到官方的pub.dev仓库：不写或者写publish_to: 'https://pub.dartlang.org'
- 发布到私有仓库：publish_to: 'https://私有仓库地址'
- 禁止发布：publish_to: 'none'

#### version
- 不携带参数构建apk：flutter build apk
  - 会使用version配的值作为VersionName和VersionCode
  - <img width="223" height="58" alt="image" src="https://github.com/user-attachments/assets/b6cb52b6-00ad-4601-a9d2-91ce27158fd0" />
  - 从打好的Apk里，可以看到刚刚的参数
  - <img width="630" height="198" alt="image" src="https://github.com/user-attachments/assets/8613290d-8f9d-4ab2-9cd2-c7dc87dea0aa" />

- 携带参数构建apk：flutter build apk --build-name "abcd" --build-number "101"
  - build-name：是安卓里的VersionName，iOS里的CFBundleShortVersionString，Windows里的major, minor, and patch parts of the product and file versions
  - build-number：是安卓里的VersionCode，iOS里的CFBundleVersion，Windows里的build suffix
  - <img width="472" height="19" alt="image" src="https://github.com/user-attachments/assets/e155d8cc-cefc-4c3e-bbb7-5aba651a7c42" />
  - 从打好的Apk里，可以看到刚刚的参数
  - <img width="602" height="267" alt="image" src="https://github.com/user-attachments/assets/51615fe3-007b-4158-8ac1-db9c13afd076" />

- 其他构建可以参考：https://github.com/codingCavalier/Daily-Life-Is-Brief/edit/main/Flutter打包Apk.md

#### environment
- sdk：用于配置项目使用的Dart版本：sdk: ^3.6.2
- flutter：用于配置项目使用的Flutter版本：flutter: ^3.27.4

#### dependencies
- 配置依赖的其他库，例如：synchronized: ^3.3.0+3
