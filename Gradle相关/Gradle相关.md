## APK编译流程

![图片](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt5ibH61k2aSyMGBcayEqf6GD69cxYhPvt9V1JoqeXHhiaoqGxU6TictAHCD9UNM7qJ1icw76dBwrICMnw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

TransForm在JavaCompiler 和 class文件之间执行

## 注解处理器

**参考**：[(70条消息) Android annotationProcessor 和 APT 运行原理_VincentWei95的博客-CSDN博客](https://blog.csdn.net/qq_31339141/article/details/93374206)

AutoService 注解   搭配ServeiceLoader   加载到对应的注解处理类  执行process()

### SPI（Service Provider Interface） 机制

