# 新增一种动态组件的时候，发现组件无法正常显示，配置项也不出来。

这个问题单纯是我自己犯傻了，居然被这问题卡了1天多，忘记了在IntentHandler里加上对应新增的Handling协议，不过这也让我知道了，少了添加这个协议会出现这样的现象(发现组件无法正常显示，配置项也不出来)。由于xcode不会报错，一时真的让人很懵逼，完全不知道是哪里出错。这次是加了CircularConfigIntent的动态配置，但忘了加上声明CircularConfigIntentHandling协议了。

参考代码：

```swift
class IntentHandler: INExtension, MediumConfigIntentHandling, SmallConfigIntentHandling, LargeConfigIntentHandling, CircularConfigIntentHandling {
    // 其他的实现代码
}
```