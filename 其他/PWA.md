<!-- TOC -->

- [[PWA 渐进式网络应用 (Progress Web Apps)](https://lavas.baidu.com/pwa)](#pwa-渐进式网络应用-progress-web-appshttpslavasbaiducompwa)
- [PWA 如何弥补和原生 App 的差距](#pwa-如何弥补和原生-app-的差距)

<!-- /TOC -->

### [PWA 渐进式网络应用 (Progress Web Apps)](https://lavas.baidu.com/pwa)

https://juejin.im/post/5a6c86e451882573505174e7

渐进式网络应用程序，是一种可以提供类似原生应用程序体验的网络应用程序 (web app).

PWA 可以再离线时应用程序能够继续运行功能。

PWA 并不是单指某一项技术，可以列结成一种思想和概念，目的就是对标原生 APP, 将 web 网站通过一系列 web 技术去优化，提升其安全性，性能，流畅性，用户体验等各方面指标，最后达到用户就在用 app 的感觉

特点:  
可靠 - 在网络不稳定,也能瞬间加载并展现
体验 - 快速响应,并且有平滑的动画响应用户操作
粘性 - 可以具有原生应用的优点,具有沉浸式的用户体验

PWA 中包含的核心功能及特性：

1. Web APP Manifest
2. Service Worker
3. Cache API 缓存
4. Push & Notification 推送与通知
5. Background Sync 后台同步
6. 响应式设计

### PWA 如何弥补和原生 App 的差距

1. 性能差异

解决：**PWA 使用 app Shell 架构模型**

- 快速加载
- 尽可能使用较少的数据
- 使用本机缓存中的静态资产
- 将内容与导航分离开来
- 检索和显示特定页面的内容 (HTML JSON)
- 缓存动态内容，APP 　 Shell 可以保证 UI 的本地化以及 API 动态加载内容，但同时不影响网络的可链接性和可检测性。用户下次访问您的应用时，应用会自动显示最新版本。无需在使用前下载新版本。为了保证首屏的加载，在内容请求完成之前，可以优先保证 App Shell 的渲染，做到和 Native App 一样的体验，App Shell 是 PWA 界面展现所需的最小资源。

2. 无法离线使用  
   解决：**Service Worker + HTTPS +Cache Api + indexedDB 等一系列 web 技术实现离线加载和缓存**

3. 无法实现推送  
   解决：**Push&Notification 实现推送与通知**

4. 无法添加到桌面

解决：**通过 manifest.json 文件配置，使得可以直接添加到手机的桌面上。**

天生优势:

1. 无需安装，无需下载，只要你输入网址访问一次，然后将其添加到设备桌面就可以持续使用。
2. 发布不需要提交到 app 商店审核
3. 更新迭代版本不需要审核，不需要重新发布审核
4. 现有的 web 网页都能通过改进成为 PWA， 能很快的转型，上线，实现业务、获取流量
5. 不需要开发 Android 和 IOS 两套不同的版本

劣势：

1. 浏览器支持还不够
