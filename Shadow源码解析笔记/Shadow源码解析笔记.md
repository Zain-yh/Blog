参考文档
https://www.jianshu.com/p/e1738998abd1
https://www.jianshu.com/p/d4488d134dc2
https://www.jianshu.com/p/10b785c8bcaa
https://www.jianshu.com/p/8838d5c32c46
开发者 https://juejin.cn/user/536217405890903/posts
android-toy/Shadow源码解析.md at master · ChenSiLiang/android-toy (github.com)


• 1.manger.apk 专门用于联通宿主apk。当宿主apk启动插件的时候，需要启动manager模块，加载 loader.apk,runtime.apk,以及业务插件apk。
• 2.loader.apk 专门用于映射坑位Activity/Broadcast/Service/ContentProvider 与 业务插件中四大组件对应的类名
• 3.runtime.apk 用于申明四大组件的类。注意runtime.apk中的AndroidManifest 中不需要声明。这些坑位需要直接声明到宿主中
• 4.插件业务生成的apk 业务的实现



PPS（继承自Service）应该代表插件进程的生命周期。插件进程应该由PPS启动而启动。

所以每个插件都代表着一个PPS，通过硬编码的key与PPS对应，在启动相应的插件时bind对应的PPS



在启动插件之前，首先需要加载 pluginManager 模块，这个模块也是以插件的形式存在的 HostApplication.getApp().loadPluginManager   返回一个SDK提供的 DynamicPluginManager

初始化Manager成功后，再通过Manager去启动插件工程 getPluginManager().enter()
在这个过程中，manager模块中的规定的方法会被执行  即 ManagerFactoryImpl 中的 buildManager，此时会返回 自己定义的 SamplePluginManager对象


开始启动插件  此时会解析Asset目录下的zip文件，包含了多个plugin插件apk，

加载runtime插件  

加载 loader插件   类似manager模块，返回一个SDK提供的DynamicPluginLoader
通过约定的接口，调用CoreLoaderFactoryImpl的build方法，返回了我们自己定义SamplePluginLoader 对象

LoadPluginBloc.loadPlugin
