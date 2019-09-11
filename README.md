## 一个基本 Java 项目

标准目录结构如下:

```text
project  
    +build  
    +src/main/java  
    +src/main/resources  
    +src/test/java  
    +src/test/resources  
```

Gradle 默认会从 src/main/java 搜寻打包源码，在 src/test/java 下搜寻测试源码。并且 src/main/resources 下的所有文件按都会被打包，所有 src/test/resources 下的文件 都会被添加到类路径用以执行测试。所有文件都输出到 build 下，打包的文件输出到 build/libs 下。

### 构建项目

Java 插件为你添加了众多任务。但是它们只是在你需要构建项目的时候才能发挥作用。最常用的就是 build 任务,这会构建整个项目。当你执行 gradle build 时，Gralde 会编译并执行单元测试，并且将 src/main/* 下面 class 和资源文件打包。


### 常用任务

- clean

删除build目录以及所有构建完成的文件

- assemble

编译并打包 jar 文件，但不会执行单元测试。

- check

编译并测试代码。

## 外部依赖

通常，一个 Java 项目拥有许多外部依赖。你需要告诉 Gradle 如何找到并引用这些外部文件。在 Gradle 中通常 Jar 包都存在于仓库中。仓库可以用来搜寻依赖或发布项目产物。下面是一个采用 Maven 仓库的例子。

### 添加Maven仓库

build.gradle文件中添加

```text
repositories {
    mavencentral()
}
```

### 添加依赖

build.gradle文件中添加

```text
dependencies {
   compile group: 'commons-collections', name: 'commons-collections', version: '3.2'
   testCompile group: 'junit', name: 'junit', version: '4.+'
}
```

### 依赖管理

build.gradle

```text
repositories {
    mavenCentral()
}

dependencies {
    compile group: 'org.hibernate', name: 'hibernate-core', version: '3.6.7.Final'
    testCompile group: 'junit', name: 'junit', version: '4.+'
}
```

上面的脚本是这么个意思。首先hibernate-core.3.6.7.final.jar是编译期必须的依赖，并且这货的依赖也会被一并加载进来。
该脚本同时还声明了项目测试阶段需要4.0版本以上的Junit。同时告诉我们去Maven 中央仓库去找这些依赖。

### 依赖配置

Gradle 中依赖以组的形式来划分不同的配置。每个配置都只是一组指定的依赖。我们称之为依赖配置 。你也可以借由此声明外部依赖。后面我们会了解到，这也可用用来声明项目的发布。
Java 插件定义了许多标准配置项。这些配置项形成了插件本身的 classpath。

- compile

编译范围依赖在所有的classpath中可用，同时他们也会被打包

- runtime

runtime 依赖在运行和测试系统的时候需要，但在编译的时候不需要。比如，你可能在编译的时候只需要 JDBC API JAR，而只有在运行的时候才需要 JDBC 驱动实现

- testCompile

测试编译阶段需要的附加依赖

- testRuntime

测试运行期需要

### 外部依赖

build.gradle

```text
dependencies {
    compile group: 'org.hibernate', name: 'hibernate-core', version: '3.6.7.Final'
}
```

外部依赖包含 group，name 和 version 几个属性。根据选取仓库的不同，group 和 version 也可能是可选的。

当然，也有一种更加简洁的方式来声明外部依赖。采用：将三个属性拼接在一起即可。"group:name:version"

如：
build.gradle
```text
dependencies {
    compile 'org.hibernate:hibernate-core:3.6.7.Final'
}
```

> 使用Maven中央仓库

build.gradle

```text
repositories {
    mavenCentral()
}
```

> 使用Maven远程仓库

build.gradle

```text
repositories {
    maven {
        url "http://repo.mycompany.com/maven2"
    }
}
```

> 一个项目可以采用多个库。Gradle 会按照顺序从各个库里寻找所需的依赖文件，并且一旦找到第一个便停止搜索。

别的仓库，略。


### 打包发布

插件对于打包提供了完美的支持，所以通常而言无需特别告诉 Gradle 需要做什么。但是你需要告诉 Gradle 发布到哪里。这就需要在 uploadArchives 任务中添加一个仓库。

> 发布到 Maven 仓库

build.gradle

```text
uploadArchives {
    repositories {
        mavenDeployer {
            repository(url: "file://localhost/tmp/myRepo/")
        }
    }
}
```

> 发布到 Maven 仓库你需要 Maven 插件的支持，当然，Gradle 也会同时产生 pom.xml 一起上传到目标仓库。

## Gradle Web 工程构建

Gradle 为 Web 开发提供了两个主要插件，War plugin 和 Jetty plugin。 其中 War plugin 继承自 Java plugin，可以用来打 war 包。jetty plugin 继承自 War plugin 作为工程部署的容器。

### 打 War 包

需要打包 War 文件，需要在脚本中使用 War plugin：

build.gradle

```text
plugins {
  id : 'war'
}
```

当你执行 gradle build 时，将会编译、测试、打包你的工程。Gradle 会在 src/main/webapp 下寻找 Web 工程文件。编译后的 classes 文件以及运行时依赖也都会被包含在 War 包中。

### web工程启动

> 采用jetty plugin启动web工程

build.gradle

```text
plugins {
  id: 'jetty'
}
```

由于 Jetty plugin 继承自 War plugin。调用 gradle jettyRun 将会把你的工程启动部署到 jetty 容器中。调用 gradle jettyRunWar 会打包并启动部署到 jetty 容器中。

> 注意：该插件从gradle4.0中已经删除，建议使用Gretty插件



