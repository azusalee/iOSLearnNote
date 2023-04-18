# 新增一种动态组件的时候，发现组件无法正常显示，配置项也不出来。

这个问题单纯是我自己犯傻了，居然被这问题卡了1天多，忘记了在IntentHandler里加上对应新增的Handling协议，不过这也让我知道了，少了添加这个协议会出现这样的现象(发现组件无法正常显示，配置项也不出来)。由于xcode不会报错，一时真的让人很懵逼，完全不知道是哪里出错。这次是加了CircularConfigIntent的动态配置，但忘了加上声明CircularConfigIntentHandling协议了。

参考代码：

```swift
class IntentHandler: INExtension, MediumConfigIntentHandling, SmallConfigIntentHandling, LargeConfigIntentHandling, CircularConfigIntentHandling {
    // 其他的实现代码
}
```

# widget怎么做到旋转动画的

使用_clockHandRotationEffect，可以做到，但这个是私有api，xcode14之后就禁止了。

_clockHandRotationEffect是一个让view跟随时针、分针、秒针旋转的方法，旋转的频率是固定的。

可以用XCode13 把_clockHandRotationEffect封装成一个公开方法，然后导出成库，这样就可以让xcode14以上用了

一个已经封装好的库，https://github.com/azusalee/ClockHandRotationKit

参考：
https://stackoverflow.com/questions/66258627/ios-clock-animations-on-homescreen-widget
