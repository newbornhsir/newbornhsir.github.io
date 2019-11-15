---
title: react-native问题汇总
date: 2017-09-19 10:25:15
tags: react-native
categories: react-native
---

## Print: Entry, ":CFBundleIdentifier", Does Not Exist

运行`react-native run-ios`报错，打开xcode,发现报错信息是`boost/iterator/iterator_adaptor.hpp’ file not found`,参考[https://medium.com/@jjerryhan/handling-third-party-build-error-on-react-native-0-46x-445f867e7a2b](https://medium.com/@jjerryhan/handling-third-party-build-error-on-react-native-0-46x-445f867e7a2b)所给的原因，重新下载和编译第三方库,再次运行成功。

## ios中fetch的http请求

ios阻止http类型的请求，修改ios项目目录下的info.plist文件，添加允许访问的域或者关闭苹果提供的安全保障

```
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSExceptionDomains</key>
        <dict>
            <key>localhost</key>
            <dict>
                <key>NSExceptionAllowsInsecureHTTPLoads</key>
                <true/>
            </dict>
        </dict>
    </dict>

```

## app ids 和 push Notifications证书创建

[链接](www.jianshu.com/p/78282e16db66)

## Exception in thread "main" java.util.zip.ZipException: zip END header not found

gradle-4.1-all 包下载出错,重新下载放到.
 

## Could not determine java version from '9.0.1'.

运行react-native run-android报错`Could not determine java version from '9.0.1'.`。jdk版本的问题，官方文档给的链接是jdk8

虽然报错，但是在AndroidStudio中可以运行成功
 
 解决办法一： 

 更换jdk 版本

 解决办法二：

 [github/react-native issue](https://github.com/facebook/react-native/issues/16536)。我更换了之后仍然报错

```
 A problem occurred configuring root project 'ReactNativeDemo'.
> Could not resolve all files for configuration ':classpath'.
   > Could not find com.android.tools.build:gradle:3.0.0.
     Searched in the following locations:
         https://jcenter.bintray.com/com/android/tools/build/gradle/3.0.0/gradle-3.0.0.pom
         https://jcenter.bintray.com/com/android/tools/build/gradle/3.0.0/gradle-3.0.0.jar

```

虽然报错，但是可以正常运行。暂时不知道原因;

猜测：用AndroidStudio打开项目会提示更新gradle到3.0，更新之后就会报这个错误，默认配置

```
distributionUrl=https\://services.gradle.org/distributions/gradle-2.14.1-all.zip
```

建议在不了解的情况下，先不要根据提示更新。


参考网上给出的解决方案，修改build.gradle,添加google().[原文链接](http://blog.csdn.net/zhouxianling233/article/details/78359217)

```
// Top-level build file where you can add configuration options common to all sub-projects/modules.

buildscript {
    repositories {
         // this is add
        google()
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.0.0'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

allprojects {
    repositories {
        // this is add
        google()
        mavenLocal()
        jcenter()
        maven {
            // All of React Native (JS, Obj-C sources, Android binaries) is installed from npm
            url "$rootDir/../node_modules/react-native/android"
        }
    }
}
```

暂时不清楚解决办法，先换回jdk8