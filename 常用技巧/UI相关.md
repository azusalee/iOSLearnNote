# 粒子效果

可以使用CAEmitterLayer做到简易的粒子效果。由于这个类可以配置的参数比较多，实际使用时还是要自己调整参数来做出一个比较好的效果。

参考代码
```swift
emitterLayer.emitterMode = .outline
emitterLayer.emitterShape = .circle
emitterLayer.emitterSize = self.bounds.size
emitterLayer.renderMode = .unordered
emitterLayer.position = CGPoint.init(x: self.bounds.width/2, y: self.bounds.height/2)
        
let radius = max(self.bounds.width, self.bounds.height)
cell.name = "explosionCell"
cell.contents = UIImage(named: "emitter_white_point")?.cgImage
cell.birthRate = 4
cell.velocity = 40
cell.lifetime = Float(radius/cell.velocity)*0.42
cell.scale = 0.2
cell.scaleRange = 0.5
cell.alphaRange = 0.1
cell.alphaSpeed = -1/cell.lifetime
// 旋转180度，配合圆形发射器可以让粒子有一种往中心集中的效果
cell.emissionLatitude = CGFloat.pi
emitterLayer.emitterCells = [cell]
```

# 通过代码让app进入后台

```swift
// 本质是调用到UIApplication的suspend方法
let targetSelect = Selector("suspend")
// 这两种方法会去除warning，效果都是一样的
// let targetSelect = #selector(URLSessionTask.suspend)
// let targetSelect = #selector(NSXPCConnection.suspend)
if UIApplication.shared.responds(to: targetSelect) {
    UIApplication.shared.perform(targetSelect)
}
```

# talbelView里有TextFiled，并tableView的数据会根据TextField的内容变化而变化，如何不让键盘自动收起

由于reloadData，会导致tableView里的cell的textField失去焦点，导致键盘收起。

所以方案有以下:

1. 只reload指定的indexPath，但如果需要整个列表刷，例如列表的行数会改变这类的，这样就不合适了。

2. 对应的textFiled点击时，另外新建一个textFiled并调用becomeFirstResponse，然后把新的盖到旧的上面，做出一种旧的textFiled在响应的假象。这个方案的表现效果是最好的，但代码也特别多，因为需要增加textField和把新textField的内容同步到旧textFiled上的逻辑，增加日后维护难度。

3. 修改布局，就是不要把textField放到tableView里，但这个其实等于是改需求了。但这个其实是最合理的，应该尽量避免这样的需求出现。

# LaunchScreen修改后实际却没有更改

这是因为iOS会缓存LaunchScreen，所以即使你改了，然后安装，其实还是会从缓存中读取，所以显示的还是旧启动页，网上找了很多，说可以通过删除缓存来做到更新，但经实际测试，发现是无效的(也有可能网上的删除路径不对)。

如果要及时更新，可以修改LaunchScreen的文件名，并修改里面用到图片的名字，而且这张图片不能放到asset里面。
