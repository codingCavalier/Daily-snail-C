1、FLAG_ACTIVITY_REORDER_TO_FRONT：如果Activity存在，则将Activity移到栈顶复用；否则创建一个新的Activity。

2、打开文件选择器，Activity在onCreate里面调，Fragment在onAttach、onCreate里面调
```kotlin
registerForActivityResult(
    ActivityResultContracts.GetContent()
) { uri ->
    
}
```
