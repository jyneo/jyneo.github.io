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

repositories 的闭包中声明的 jcenter 配置，是一个代码托管仓库，很多 Android 开源项目都托管在 jcenter 上，声明了这行配置，就可以在项目中引用任何 jcenter 上的开源项目了。dependencies 闭包中使用 classpath 声明了一个 gradle 插件，后面的部分是插件的版本号。

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
