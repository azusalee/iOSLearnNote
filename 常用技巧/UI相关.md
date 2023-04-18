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
