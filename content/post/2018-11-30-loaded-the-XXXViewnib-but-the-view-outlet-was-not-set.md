---
layout: post

title: loaded the "XXXView" nib but the view outlet was not set

date: 2018-11-30 10:00:00

categories: ["iOS"]

tags:

mathjax: true
---

关于`[UIViewController _loadViewFromNibNamed:bundle:] loaded the "XXXView" nib but the view outlet was not set`这个错误的原因，一般分为两种情况：

1. **第一种**

   当控制器的`view`是通过`xib`加载的，但是在`xib`中并没有绑定`File's Owner`,或者没有对`File's Owner`中的`view`进行连线。

1. **第二种**

   这种问题比较容易被忽略，当时也会出现。

   当`xib`的描述的`view`的名称跟控制器很像的时候，比如`LoginViewController`和`LoginView`，就会出现。

   > 原因：
   >
   > ​	我们创建控制器：
   >
   > ```objectivec
   > LoginViewController *loginVC = [LoginViewController alloc] init];
   > ```
   >
   > `init`内部会先去寻找有没有跟`LoginViewController`相同的`xib`文件，如果没有，再去找有没有少了`controller`的`xib`,如果有就去加载，这样就会报错。
