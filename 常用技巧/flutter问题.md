# 数数thinking_analytics在android编译不过

thinking_analytics版本为3.0.5

在iOS中能正常编译

在android编译会报错，是gradle的错误，gradle在8.0后需要指定namespace

解决方法

在/android/build.gradle文件中添加以下代码

```
# 这句是原本有的
rootProject.buildDir = "../build"

# 这是新加的，注意顺序
subprojects {
    afterEvaluate { project ->
        if (project.hasProperty('android')) {
            project.android {
                if (namespace == null) {
                    namespace project.group
                }
            }
        }
    }
}
```
