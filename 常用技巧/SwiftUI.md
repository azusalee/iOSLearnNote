# 使用UIHostingController里面的swiftui的view会偏移

这个问题是发生在iOS16以下的系统，目前只找到一个勉强算是解决的方法，但不是特别好，而且在某些情况下也是无效的。

我这边测试是在scrollView里面加UIHostingController才会出现，这个问题比较随机，有些地方会出现，有些又不出现。

解决用文字不太好说明，总之就是参考下面代码，把UIHostingController改用自定义的MyHostingController。

代码如下

```swift
// 针对ios16以下的情况，在scrollView里加入MyHostingController的view，有可能使swiftui的view偏移，偏移是随机的
// 这里通过使用此controller，可以一定程度解决这个问题，如果对MyHostingController的view的transform操作了，那么这里依然不能解决偏移问题
class MyHostingController<Content>: UIHostingController<Content> where Content: View {
    
    private var heightConstraint: NSLayoutConstraint?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        if #available(iOS 16.0, *) {
            
        } else {
            heightConstraint = self.view.heightAnchor.constraint(equalToConstant: 0)
        }
        
    }
    
    override func viewDidLayoutSubviews() {
        super.viewDidLayoutSubviews()
        if #available(iOS 16.0, *) {
            
        } else {
            self.view.sizeToFit()
            heightConstraint?.constant = self.view.bounds.height
            heightConstraint?.isActive = true
        }
    }
}

```
