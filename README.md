# 版本说明
由于Yoga使用BUCK构建，而实际项目中使用BUCK构建的很少，如果想在一般项目中，尤其是android项目，使用Yoga会很麻烦，会遇到的问题如下：

1、Yoga没有提供android项目所需的.so文件，需要自己生成。

2、要想使用Yoga源码生成.so，就需要使用BUCK，需要安装BUCK编译环境。

3、不是所有版本的源码使用BUCK编译都能成功，Yoga也没有说明，需要挨个尝试找到能用的历史版本。

4、编译arm版本时会遇到一个代码中的错误，需要修改一下代码。

5、Yoga运行需要NDK中的一个依赖库，打包的时候打不进去，需要自己手动从NDK中找出来加进去。

下面详细说明解决办法。

## 安装BUCK编译环境
[buck](https://buckbuild.com)的安装过程情参考官网。需要注意的问题如下：

1、Buck的环境变量添加了没有

2、android SDK／NDK的环境变量添加了没有

3、gcc有没有，版本够不够新

## checkout到能用的版本
如果你clone了最新代码，BUCK环境没有问题，准备生成.so了，恭喜，肯定失败，为什么，不知道，没有找到任何说明。

查看git上的所有tag，可以看到历史版本，有两种，一种是版本号命名的，用不了，另一种是日期命名的，基本上都可用，选一个checkout过去，以后不要轻易变，因为不同版本的java代码和BUCK文件可以会有区别，方法调用和生成命令都会变。这个分支是基于v2017.02.07.00版本，这是目前最新的能用的版本。

## 修改代码
在生成arm版本.so时会报错：

lib/fb/jni/Exceptions.cpp文件的'void rethrow_if_nested()'方法，找不到'std::nested_exception'。

gcc已是最新版，只能把这两句注释掉：

void rethrow_if_nested() {

  try {

    throw;

  // } catch (const std::nested_exception& e) {

  //   e.rethrow_nested();

  } catch (...) {

  }

}

## 生成so
这个过程参考了这个项目[AndYogaExample](https://github.com/koudle/AndYogaSample)，帮助很大，但是不知道它使用的是哪个版本，生成命令不一样。

本版本的生成命令：

buck build //:yoga#android-arm,shared

buck build //java:jni#android-arm,shared

buck build //lib/fb:fbjni#android-arm,shared

注意：shared是必需的。

除了这三个so，还需要从	

android-ndk/sources/cxx-stl/gnu-libstdc++/4.9/libs/

目录下拿到libgnustl_shared.so放到工程里。

## 使用yoga
执行完以上过程，准备工作才算做好，可以开始使用yoga开发自己的项目了，具体使用方法可以参考上面那个项目和作者的文章：

[Facebook YOGA初探](https://koudle.github.io/2017/04/11/yoga/)

## 总结
Yoga就是一坨屎！

