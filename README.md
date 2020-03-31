
### Gradle 构建生命周期
无论什么时候执行Gradle构建，都会运行3个不同的生命周期阶段：初始化、配置、执行。

1. 初始化（Initialization）
Gradle为每个项目创建一个Project实例，在多项目构建中，Gradle会找出哪些项目依赖需要参与到构建中。
2. 配置（Configuration）

执行所有项目的构建脚本，也就是执行每个项目的build.gradle文件。这里需要注意的是，task里的配置代码也会在这个阶段执行。
执行（Execution）
3. Gradle按照依赖顺序依次执行task。

### 创建Task

`org.gradle.api.Project`类提供了创建`org.gradle.api.Task`的方法：

1. 
```java
/**
 * 使用指定的名称创建一个任务，并将该任务加入到当前的project。
 * 当该任务被添加到project中以后，该任务可以作为project的一个属性使用，
 * 所以你可以在你的build文件中使用指定的名称来引用该任务。
 *
 * 如果在当前project中已经存在同名的任务，则抛出异常。
 * @param name 要创建的任务的名称
 * @return 创建好的任务
 * @throws InvalidUserDataException 如果在当前project中已经存在同名的任务，则抛出异常。
 */
Task task(String name) throws InvalidUserDataException;
```
使用方式
```
def myTask = task("firstTask")
println(myTask.name)
```
2. 使用指定的action配置创建的任务
```
/**
 * 使用指定的名称创建一个任务，并将该任务加入到当前的project。
 * 在创建的任务返回前，会使用指定的action来配置该任务。
 *
 * @param name The name of the task to be created
 * @param configureAction 用来配置任务的 action
 * @return The newly created task object
 * @see TaskContainer#create(String, Action)
 * @since 4.10
 */
@Incubating
Task task(String name, Action<? super Task> configureAction);
```
使用方式
```
def secondTask = task("secondTask", new Action<Task>() {
    @Override
    void execute(Task task) {
        task.setDescription("我是SecondTask，美滋滋")
    }
})
println(secondTask.description)
```
3. 使用指定的闭包配置创建的任务
```
/**
 * 使用指定的名称创建一个任务，并将该任务加入到当前的project。
 * 在创建的任务返回前，会使用指定的closure来配置该任务。 
 *
 * @param name The name of the task to be created
 * @param configureClosure 指定的闭包
 * @return The newly created task object
 */
Task task(String name, Closure configureClosure);
```
```
def thirdTask = task("thirdTask") {
    task -> task.setDescription("我是thirdTask，美滋滋")
}
println(thirdTask.description)
```
4. 
```java
/**
 *使用指定的名称创建一个任务，并将该任务加入到当前的project。
 * 可以传递一个Map创建选项来控制任务的创建。可用的选项如下表所示：
 *
 * @param args The task creation options.
 * @param name The name of the task to be created
 * @return The newly created task object
 */
Task task(Map<String, ?> args, String name) throws InvalidUserDataException;
```
|可用选项|说明|默认值|
|:---:|:---:|:---:|
|org.gradle.api.Task#TASK_TYPE|要创建的任务类型|org.gradle.api.DefaultTask|
|org.gradle.api.Task#TASK_OVERWRITE|是否替代已有的任务|false|
|org.gradle.api.Task#TASK_DEPENDS_ON|依赖的任务|无|
|org.gradle.api.Task#TASK_ACTION|添加到任务上的closure或Action|null|
|org.gradle.api.Task#TASK_DESCRIPTION|任务描述|null|
|org.gradle.api.Task#TASK_GROUP|任务所在的组|null|

Task类的部分成员变量
```
public interface Task extends Comparable<Task>, ExtensionAware {
    String TASK_NAME = "name";

    String TASK_DESCRIPTION = "description";

    String TASK_GROUP = "group";

    String TASK_TYPE = "type";

    String TASK_DEPENDS_ON = "dependsOn";

    String TASK_OVERWRITE = "overwrite";

    String TASK_ACTION = "action";
    //...
}
```

使用方式
```
def map = [type: DefaultTask, overwrite: false, dependsOn: [secondTask, thirdTask]]
map["description"] = "这是任务描述"
map["group"] = "任务所在的组"

def forthTAsk = task(map, "forthTask")

println(forthTAsk.description)
println(forthTAsk.group)
```

在某些版本上`overwrite`选项已经过时了，如果该选项为true的话，会产生错误。所以尽量不要替代现有的任务，可以考虑创建新的任务。
```
Replacing an existing task that may have already been used by other plugins is not supported.
Use a different name for this task ('forthTask').
```
5. 这个方法比上面的方法多了一个闭包参数
```
Task task(Map<String, ?> args, String name, Closure configureClosure);
```
```
def map = [type: DefaultTask, overwrite: false, dependsOn: [secondTask, thirdTask]]
map["description"] = "这是任务描述"
map["group"] = "任务所在的组"

def forthTAsk = task(map, "forthTask"){
    println("forthTask 的闭包配置")
}
```



### 执行Task

gradle taskName
```
gradle helloWorld

```
gradle -q taskName，加上`-q`选项可以减少一些不必要的日志输出


参考链接：
* [Android Gradle学习(一)：Gradle基础入门](https://www.jianshu.com/p/e26236943dd6)