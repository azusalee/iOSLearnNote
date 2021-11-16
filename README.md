# iOSLearnNote
iOS开发过程中的问题与解决笔记

1. 多语言国际化相关 - 如何找出代码中的中文硬编码

使用Xcode的全局搜索，切换搜索模式到 Find > Regular Express，进行正则搜索
"[^"]*[\u4E00-\u9FA5]+[^"\n]*?"
