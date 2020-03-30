
### Gradle 构建生命周期
无论什么时候执行Gradle构建，都会运行3个不同的生命周期阶段：初始化、配置、执行。

1. 初始化（Initialization）
Gradle为每个项目创建一个Project实例，在多项目构建中，Gradle会找出哪些项目依赖需要参与到构建中。
2. 配置（Configuration）

执行所有项目的构建脚本，也就是执行每个项目的build.gradle文件。这里需要注意的是，task里的配置代码也会在这个阶段执行。
执行（Execution）
3. Gradle按照依赖顺序依次执行task。

### 执行Task

gradle taskName
```
gradle helloWorld

```
gradle -q taskName，加上`-q`选项可以减少一些不必要的日志输出


参考链接：
* [Android Gradle学习(一)：Gradle基础入门](https://www.jianshu.com/p/e26236943dd6)