# UIActivityViewController

这是iOS可以用来做分享的一个类

## 如何自定义header

这里的header是指iOS13之后，UIActivityViewController顶部会有个header显示一些分享相关的信息，图标，标题，副标题等。

自定义这部分的数据需要使用UIActivityItemSource这个协议，具体使用，参考以下代码

```swift
    class ShareActivityItemSource: NSObject, UIActivityItemSource {
    
        var metadata: LPLinkMetadata?
        
        init() {
            self.setupData()
        }
        
        func setupData() {
            let metadata = LPLinkMetadata()
            // 标题可以直接这样设置
            metadata.title = "小倒数"
            // 图标可以这样设置，这里的图标必须要放在项目的目录里面，而不能放在asset，而且必须要用url
            // 例如用NSItemProvider(object: UIImage(named: "iconName"))，这样用UIImage对象是无效的
            metadata.iconProvider = NSItemProvider(contentsOf: Bundle.main.url(forResource: "shareIcon", withExtension: "png"))
            // 设置url，副标题显示的就是url的信息，url是网址，显示的是域名；url是本地文件路径，显示的就是文件名
            // 暂时没有找到可以随意改变副标题的方法
            metadata.originalURL = url
            self.metadata = metadata
        }
        
        func activityViewControllerLinkMetadata(_ activityViewController: UIActivityViewController) -> LPLinkMetadata? {
            return self.metadata
        }
    }
```

UIActivityViewController使用方法

```swift
let items = [ShareActivityItemSource()]
let controller = UIActivityViewController.init(activityItems: items, applicationActivities: nil)
self.present(controller, animated: true)
```

## 如何直接分享图片

直接把UIImage对象放入items里就可以了

```swift
let items = [UIImage(named: "iconName")]
let controller = UIActivityViewController.init(activityItems: items, applicationActivities: nil)
self.present(controller, animated: true)
```

## 如何分享Url

直接把Url对象放入items里就可以了，一般还需要自己加上一个text和image来显示标题和图标，如果分享的是app的appStore链接，会自动解析出链接的标题、图标、副标题放入header显示。

```swift
let items = [URL(String: "https://xxxx"), "标题", UIImage(named: "AppIcon")]
let controller = UIActivityViewController.init(activityItems: items, applicationActivities: nil)
self.present(controller, animated: true)
```
