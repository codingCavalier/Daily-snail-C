### 1. 分享一个遇到的坑
- 推荐一个好用的WebView：https://github.com/Amir-yazdanmanesh/Android-ProfessionalWebView
- 安卓8.0以后，WebView进入了多进程模式：
<img width="686" height="62" alt="image" src="https://github.com/user-attachments/assets/be5f1766-465d-4395-ba70-030df0c500ee" />

- 遇到的坑是：多进程起来后，DisplayMetrics的xdpi会变回去，这就导致刚刚做的页面适配会面目全非，需要及时修正。
- 我的解决办法是：在获取Resources的时候，每次都进行适配操作。
