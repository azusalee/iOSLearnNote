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

# Widget里同样的字体跟在app里显示时不一样

有问题机型
15 pro max
15 plus
14 pro max
14 plus
13 pro max

暂时并没有测试所有机型，估计所有promax和plus都有这样的问题，目前发现使用自定义字体时，widget里的字体大小比在app里显示的大 1.07 倍左右，导致布局位置的计算不准确。但使用系统的字体时，大小又是一样的（？？挺莫名其妙的）

处理方法，判断如果机型是上述机型且是在widget显示，缩小字体大小，具体就是判断屏幕宽高为430*932和428*926。
经研究，发现是字体创建的方法有问题，使用Font.custom(name, fixedSize: size)的方法创建就正常了。

