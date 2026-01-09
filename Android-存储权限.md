#### 概述
- 4.0 - 4.3：应用在**安装时一次性申请所有权限**，用户只能选择全部接受或拒绝安装。
  - 存储权限包括 WRITE_EXTERNAL_STORAGE（写权限）和 READ_EXTERNAL_STORAGE（读权限），申请后即可全局访问外部存储。
- 4.4：引入应用专属目录概念，应用可以**无需权限访问 `sdcard/Android/data/应用包名/` 目录**，但访问公共目录仍需权限。同时区分了内置SD卡和外置TF卡的权限管理。
- 6.0：引入动态权限机制，应用在**运行时请求敏感权限**，用户可以选择性授予或拒绝单项权限。存储权限被归类为危险权限（即敏感权限），需要动态申请。
  - activity.requestPermissions(arrayOf(android.Manifest.permission.WRITE_EXTERNAL_STORAGE), 110)
- 10.0：**引入分区存储**，应用默认只能访问自身专属目录（4.4已有）和通过MediaStore API访问媒体文件，**无法直接通过文件路径访问公共目录（即file://废除，全部改用content://）**。
  - **强制使用 MediaStore 访问图片、视频、音频**，必须通过 MediaStore.Images、MediaStore.Video、MediaStore.Audio等 API
  - 需要 READ_EXTERNAL_STORAGE 权限才能通过 MediaStore 读取媒体文件
  - **可通过 requestLegacyExternalStorage=true 暂时禁用分区存储（仅Android 10.0有效）**
- 11.0：**强制使用分区存储，requestLegacyExternalStorage 标志失效**，所有应用必须适配分区存储。WRITE_EXTERNAL_STORAGE 权限基本失效，只能用于旧版API兼容。
  - 引入 MANAGE_EXTERNAL_STORAGE 权限，允许全面访问存储，但需用户手动授权且Google Play审核严格，仅限文件管理器、备份工具等特殊应用使用，普通应用不用想了
- 13.0：**权限细分**，废弃 READ_EXTERNAL_STORAGE 和 WRITE_EXTERNAL_STORAGE，应用必须按需申请细分权限，系统会根据应用请求的媒体类型显示不同的权限对话框。
  - android.Manifest.permission.READ_MEDIA_IMAGES：图片访问权限
  - android.Manifest.permission.READ_MEDIA_VIDEO：视频访问权限
  - android.Manifest.permission.READ_MEDIA_AUDIO：音频访问权限
 
  #### MediaStore
  - 1.0：引入
  - 6.0：推荐使用
  - 10.0：应用默认只能通过 MediaStore 访问媒体文件（图片视频音频），无法直接通过文件路径访问公共目录。requestLegacyExternalStorage=true 可以临时避开分区存储
  - 11.0：必须通过 MediaStore 访问媒体文件（图片视频音频），requestLegacyExternalStorage=true 失效
  - 13.0：MediaStore 权限细分
  - 使用：
  ```kotlin
  val cursor = context.contentResolver.query(
    MediaStore.Images.Media.EXTERNAL_CONTENT_URI,
    projection,
    selection,
    selectionArgs,
    sortOrder
)

cursor?.use {
    val idColumn = it.getColumnIndexOrThrow(MediaStore.Images.Media._ID) // id是数据库字段里的第几列
    while (it.moveToNext()) {
        val id = it.getLong(idColumn) // 拿出图片的id
        val contentUri = ContentUris.withAppendedId(
            MediaStore.Images.Media.EXTERNAL_CONTENT_URI, id
        ) // 拼成最终uri
    }
}
  ```
