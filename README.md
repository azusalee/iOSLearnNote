# iOSLearnNote

iOS开发过程中的问题与解决笔记

1. 多语言国际化相关 - 如何找出代码中的中文硬编码

使用Xcode的全局搜索，切换搜索模式到 Find > Regular Express，进行正则搜索
"[^"]*[\u4E00-\u9FA5]+[^"\n]*?"

2. 多语言国际化相关 - 多语言key命名

可以分为，通用的key和业务的key；
通用的key，主要对一些常用的词语进行多语言化，一般会有比较多地方重复使用，跟业务没有较强关系，例如: "确定"、"取消"等词，key命名为"confirm"、"cancel"。
业务的key，主要针对业务设置的词句，一般只在特定的地方使用，几乎不重复使用，key的命名可以与使用地方相关，例如主页的标题的key可以命名为"home_title"。
