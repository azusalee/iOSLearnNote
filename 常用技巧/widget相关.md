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

# iOS17Widget的交互

iOS17后，widget增加交互的功能，只支持button和toggle控件，点击后可以不进入app执行一些代码，通过AppIntents实现。由于代码是在 widget 里执行的，所以最多只能更新数据，打开app之类的功能是做不到的，以及让主app执行代码也是不行的。

但发现部分app能做到点击widget按钮控制主app里的音乐播放。但有以下限制：

- app要能在后台运行(通过音乐播放或者总是定位的权限可以做到)

经一番测试大概猜测，其做法是这样的，点击widget的按钮后更新数据，主app里就常驻一个定时器，定时检测这个数据的值是否有变化，然后根据这些数据的变化控制播放逻辑。估计定时器的执行间隔为0.2~0.5秒。

# XCode15运行iOS16.0及以下的系统显示不出小组件

目前有几个操作可能会导致这样的问题，大多数问题看上去像是XCode有bug。以下问题都是在模拟器上测试的，由于没有iOS16以下的设备，所以真机的情况还是不太明确。

1.other Linker Flags 有值。
2.Excluded Architectures填arm64。(这个有些麻烦，有些第三方sdk必须要去除arm64才能在模拟器跑，使用Rosetta模式也是会显示不出widget)
3.添加了intentdefinition文件，但没有添加IntentExtend的target。
4.添加了widget，但没有在WidgetBundle中使用。(但有时又不会有问题)
5.Minimum Deployments的版本没有设置对。
6.有些代码会莫名的跑不了，但加点回车，调整下位置就行了。
7.不能在@main的widgetbundle里直接写版本判断分支语句。可以参考以下写法

```swift

@main
struct AppWidgetBundle: WidgetBundle {
    var body: some Widget {
        MyWidgetBundle().body
    }
}

// 必须要这样写，不然iOS16不显示小组件列表(应该是xcode或者iOS16的bug)
struct MyWidgetBundle: WidgetBundle {
    var body: some Widget {
        if #available(iOS 16.0, *) {
            return WidgetBundleBuilder.buildBlock(SmallWidget(),
                                                  MediumWidget(),
                                                  LargeWidget(),
                                                  WeekMonthYearWidget(),
                                                  MutiCountInfoSmallWidget(),
                                                  MutiCountInfoMediumWidget(),
                                                  MutiCountInfoLargeWidget(),
                                                  SingleCountInfoCircleWidget(),
                                                  SingleCountInfoRectangularWidget(),
                                                  MutiCountInfoRectangularWidget()
            )
        } else {
            return WidgetBundleBuilder.buildBlock(SmallWidget(),
                                                  MediumWidget(),
                                                  LargeWidget(),
                                                  WeekMonthYearWidget(),
                                                  MutiCountInfoSmallWidget(),
                                                  MutiCountInfoMediumWidget(),
                                                  MutiCountInfoLargeWidget()
            )
        }
    }
}
```

# 小组件url跳转

在小组件可以通过.widgetURL或者Link()来指定url，在点击时把对应url带到宿主app的回调方法里，从而分辨出点击的是什么。
其中widgetURL一个组件里只能有一个，而Link可以有多个。

在iOS16，iOS17上测试发现，使用widgetURL时，有时刷新组件然后不用widgetURL，但点击时依然有效。

参考代码

```
    var body: some View {
            VStack(spacing: 10) {
                Image("vip_crown")
                Text("解锁会员即可使用")
                    .font(.custom("PingFangSC", fixedSize: 12).weight(.semibold))
                    .foregroundColor(Color.white)
            }
            .frame(maxWidth: .infinity, maxHeight: .infinity)
            .background(Color.black.opacity(0.6))
        // 使用widgetURL，有时候即时刷新了小组件，但这个跳转的逻辑还会留下(感觉是系统的bug)，所以只能用Link
        .widgetURL(URL(string: "\(UrlRouteScheme.pomoup.rawValue)://\(UrlRouteHost.showVipVC)")!)
    }
```

# iOS17小组件不显示在列表或者显示空白

这个问题，重启手机可以解决。

经测试发现，每次安装或者更新app时，都有概率会让这个app的小组件显示出问题，只能重启手机才能解决。目前看上去像是XCode15或者iOS17的bug。

经使用一段时间后发现，如果一段时间没重启手机(大概1天左右)，然后这时候再安装或者更新app，而那个app又是XCode15打包的，那么会有比较高的概率复现上述问题，而且只要出现一次，后面再装的app大概率也会出现该问题。
