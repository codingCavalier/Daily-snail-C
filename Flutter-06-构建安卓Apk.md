### 构建命令
- flutter build apk --build-name "abcd" --build-number "101"
- <img width="472" height="19" alt="image" src="https://github.com/user-attachments/assets/e155d8cc-cefc-4c3e-bbb7-5aba651a7c42" />
- build-name：是安卓里的VersionName，iOS里的CFBundleShortVersionString，Windows里的major, minor, and patch parts of the product and file versions
- build-number：是安卓里的VersionCode，iOS里的CFBundleVersion，Windows里的build suffix
- 其他构建可以参考：https://github.com/codingCavalier/Daily-Life-Is-Brief/edit/main/Flutter打包Apk.md
- 从打好的Apk里，可以看到刚刚的参数
- <img width="602" height="267" alt="image" src="https://github.com/user-attachments/assets/51615fe3-007b-4158-8ac1-db9c13afd076" />
