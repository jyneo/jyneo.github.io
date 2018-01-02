---
layout: post
title: Android项目结构解析
key: 20170102
tags: android
category: blog
date: 02 01, 2018
modify_date: 02 01, 2018
---

## Project 模式的项目结构

虽然接触 Android 开发有一段时间了，但是对于项目结构中一些不常编辑的文件，到了真正要使用的时候，还是不太清楚它们的用法，现在记录一下。

### .gradle 和 .idea

这两个目录下放置的都是 Android studio 自动生成的一些文件，我们平常在开发的时候不需要关心，也不要去手动编辑。
<!--more-->

### app

这个目录包含了项目中的代码、资源等内容，我们的开发工作基本上在这个目录下进行，下面来详细地看一下：

- build: 编译时自动生成的文件，主要包括 generated、intermediates、outputs、tmp等文件夹。

- libs: 项目中需要引用的第三方库包。

- androidTest: 编写测试用例，对项目进行自动化测试。

- java: 放置 Java 源代码。

- res: 项目的资源文件，主要包括 layout (布局文件)、values (字符串资源)、drawable (图片资源)、mipmap (应用图标)等资源。

- AndroidManifest.xml: 整个 Android 项目的配置文件，项目的四大组件注册、给应用程序添加权限声明都在这里进行。

- .gitignore: 将 app 模块内指定的目录或文件排除在版本控制之外。

- app.iml: IntelliJ IDEA 项目自动生成的文件，无需修改。

- build.gradle: 这是 app 模块的构建脚本，里面指定了很多与项目构建相关的配置，下面会详细分析 gradle 构建脚本中的具体内容。 

- proguard-rules.pro: 这个文件用于指定项目代码的混淆规则，当代码开发完成后打包成安装包文件，如果不希望代码被别人破解，通常会将代码进行混淆，从而让破解者难以阅读。

### build

编译自动生成的文件。

### gradle

gradle wrapper 的配置文件，Android studio 会自动根据本地的缓存情况决定是否需要联网下载 gradle，内容示例如下：

```
#Tue Jan 02 14:25:29 CST 2018
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-4.1-all.zip
```

### .gitignore

将指定的目录或文件排除在版本控制之外。

### build.gradle

项目全局的 gradle 构建脚本。

### gradle.properties

全局的 gradle 构建脚本，这里配置的属性会影响到项目中所有的 gradle 编译脚本。

### gradlew 和 gradlew.bat

用来在命令行执行 gradle 命令的，其中 gradlew 是在 Linux 或 Mac 系统下使用的，gradlew.bat 是在 Windows 系统下使用的。

### app.iml

iml 文件是 IDEA 项目都会自动生成的一个文件，用于标识这是 IDEA 项目。

### local.properties

用于指定本地的 Android sdk 路径，通常会自动寻找本机中 sdk 的路径。

### setting.gradle

用于指定项目中所有引入的模块。

## 详解 build.gradle 文件

Android studio 是采用 Gradle 来构建项目的，它是一种基于 Groovy 的领域特定语言(DSL)来声明项目设置。一个 Android 项目有两个 build.gradle 文件，一个是在外层目录下，一个是在 app 目录下。

### 外层 build.gradle

外层的 build.gradle 是全局的项目配置，代码如下所示：
```
// Top-level build file where you can add configuration options common to all sub-projects/modules.

buildscript {
    
    repositories {
        google()
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.0.1'
        

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

allprojects {
    repositories {
        google()
        jcenter()
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}
```
repositories 的闭包中声明的 jcenter 配置，是一个代码托管仓库，很多 Android 开源项目都托管在 jcenter 上，声明了这行配置，就可以在项目中引用任何 jcenter 上的开源项目了。dependencies 闭包中使用 classpath 声明了一个 gradle 插件，后面的部分是插件的版本号。

### 内层的 build.gradle

app 目录下的 build.gradle 是模块的构建脚本，代码如下所示：
```
apply plugin: 'com.android.application'

android {
    compileSdkVersion 26
    buildToolsVersion "26.0.3"
    defaultConfig {
        applicationId "com.jyneo.androidtest"
        minSdkVersion 19
        targetSdkVersion 26
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.android.support:appcompat-v7:26.1.0'
    implementation 'com.android.support.constraint:constraint-layout:1.0.2'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'com.android.support.test:runner:1.0.1'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.1'
}
```
- 第一行应用了一个插件，一般有两种值可以选：com.android.application 表示这是一个应用程序模块，com.android.library 表示这是一个库模块。应用程序模块和库模块的最大区别在于，应用程序模块是可以直接运行的，而库模块只能作为代码库依附于别的应用程序模块来运行。

- 接下来是一个 android 闭包，用来配置项目构建的各种属性。

    - compileSdkVersion 用于指定项目的编译版本，这里指定为26表示使用 Android 8.0 系统的 SDK 编译。

    - buildToolVersion 用于指定项目构建工具的版本。

    - defaultConfig 闭包可以对项目的更多细节进行配置：

        - applicationId 用于指定项目的包名，之前创建项目的时候已经指定过了，如果想要修改，就是在这里进行修改。

        - minSdkVersion 用于指定项目最低兼容的 Android 系统版本，这里指定为19表示最低兼容到 Android 5.0 系统。

        - targetSdkVersion 指定的值表示你在该目标版本上已经做过了充分的测试。

        - versionCode 用于指定项目的版本号。

        - versionName 用于指定项目的版本名。

    - buildTypes 闭包用于配置生成安装文件的相关配置，通常只有两个子包，一个是 debug，用于指定生成测试版安装文件的配置，一个是 release，用于指定生成正式版安装文件的配置。另外，debug 闭包是可以省略不写的。

        - minifyEnabled 用于指定是否对项目的代码进行混淆。

        - proguardFiles 用于指定混淆时使用的规则文件，这里指定了两个文件，第一个 proguard-android.txt 是在 Android SDK 目录下的，里面是所有项目通用的混淆规则，第二个 proguard-rules.pro 是在当前项目的根目录下的，里面可以编写当前项目特有的混淆规则。

- dependencies 闭包可以指定当前项目所有的依赖关系，通常 Android studio 项目一共有三种依赖：本地依赖，库依赖和远程依赖。本地依赖可以对本地的 Jar 包或目录添加依赖关系，库依赖可以对项目中的库模块添加依赖关系，远程依赖则是对 jcenter 库上的开源项目添加依赖关系。

    - implementation fileTree 就是一个本地依赖声明，它表示将 libs 目录下的所有 .jar 后缀的文件都添加到项目的构建路径中。

    - implementation 'com.android.support:appcompat-v7:26.1.0' 就是一个远程声明，'com.android.support:appcompat-v7:26.1.0' 就是一个标准的依赖库格式。

    - implementation project(':依赖库名称')

    - androidTestImplementation 用来声明测试用例库的。

<font color="red">注意：</font>

> 最新版的 Gradle plugin (Android Gradle plugin 3.0)需要你指出一个 module 的接口是否对外暴露其依赖 lib 的接口。基于此，可以让项目构建时，gradle 可以判断哪个需要重新编译。因此，老版本的构建关键字 compile 被废弃了，改成了这两个：

> api: 同 compile 作用一样，即认为本 module 将会泄露其依赖的 module 的内容。

> implementation: 本 module 不会通过自身的接口向外部暴露其依赖 module 的内容。 

由此，当使用的 module 的接口发生变化的时候，我们可以明确地告诉 gradle 去重新编译哪一个 module。

```
dependencies {
//当 helper 接口发生变化时，需要重新编译本 module 以及所有使用本 module 的 module
api project(':helper') 
// 仅当 landscapevideocamera 发生变化时，重新编译本 module
implementation project(':landscapevideocamera:1.0.0')
}
```

<font color="green">总结</font>

理论上，你可以将原来工程中的 compile 完全替换为现在的 api，但是一旦依赖发生变化，将会使所有的 module 重新编译，造成编译过长。更好的方式就是使用implementation 来进行依赖，这会大大改善工程的构建时间。只有你明确要向外部暴露所依赖lib的接口时，才需要使用 api 依赖，整体来说，会减少很多重新编译。