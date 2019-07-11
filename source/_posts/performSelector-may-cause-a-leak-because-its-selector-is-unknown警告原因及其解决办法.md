title: performSelector may cause a leak because its selector is unknown警告原因及其解决办法
entitle: 'performSelector-warning-fix'
author: 小苏
avatar: /images/favicon.png
authorLink: 'https://www.tangkunyin.com'
authorAbout: 'https://about.tangkunyin.com'
authorDesc: 一个写代码的「伪文人」
categories: 前端
timestamp: 1528016144
date: 2018-06-03 16:55:44
tags:
    - iOS
keywords: iOS, performSelector
description: 来自2016.7.27的笔记："performSelector may cause a leak because its selector is unknown"警告原因及其解决办法
photos:
---

### 问题描述

项目中使用到了从字符串创建选择器，编译时发现警告："performSelector may cause a leak because its selector is unknown"（因为performSelector的选择器未知可能会引起泄漏），为什么在ARC模式下会出现这个警告？

经过搜索后，在Stackoverflow上发现了一个令人满意的答案。见http://stackoverflow.com/questions/7017281/performselector-may-cause-a-leak-because-its-selector-is-unknown。

### 原因

在ARC模式下，运行时需要知道如何处理你正在调用的方法的返回值。这个返回值可以是任意值，如void,int,char,NSString,id等等。ARC通过头文件的函数定义来得到这些信息。所以平时我们用到的静态选择器就不会出现这个警告。因为在编译期间，这些信息都已经确定。

如：

```
[someController performSelector:@selector(someMethod)];

- (void)someMethod
{
  //bla bla...
}
```
而使用[someController performSelector: NSSelectorFromString(@"someMethod")];时ARC并不知道该方法的返回值是什么，以及该如何处理？该忽略？还是标记为ns_returns_retained还是ns_returns_autoreleased?

解决办法

1.使用函数指针方式

```
SEL selector = NSSelectorFromString(@"someMethod");
IMP imp = [_controller methodForSelector:selector];
void (*func)(id, SEL) = (void *)imp;
func(_controller, selector);
```
当有额外参数时，如

```
SEL selector = NSSelectorFromString(@"processRegion:ofView:");
IMP imp = [_controller methodForSelector:selector];
CGRect (*func)(id, SEL, CGRect, UIView *) = (void *)imp;
CGRect result = func(_controller, selector, someRect, someView);
```

2.使用宏忽略警告

```
#pragma clang diagnostic push 
#pragma clang diagnostic ignored "-Warc-performSelector-leaks" 
   [someController performSelector: NSSelectorFromString(@"someMethod")]
#pragma clang diagnostic pop
```

通过使用#pragma clang diagnostic push/pop，你可以告诉Clang编译器仅仅为某一特定部分的代码来忽视特定警告。

如果需要忽视的警告有多处，可以定义一个宏

```
#define SuppressPerformSelectorLeakWarning(Stuff) \
    do { \
        _Pragma("clang diagnostic push") \
        _Pragma("clang diagnostic ignored \"-Warc-performSelector-leaks\"") \
        Stuff; \
        _Pragma("clang diagnostic pop") \
    } while (0)
    
    ```
在产生警告也就是performSelector的地方用使用该宏，如

```
SuppressPerformSelectorLeakWarning(
    [_target performSelector:_action withObject:self]
);
```
如果需要performSelector返回值的话，

```
id result;
SuppressPerformSelectorLeakWarning(
    result = [_target performSelector:_action withObject:self]
);
```
3.使用afterDelay

```
[self performSelector:aSelector withObject:nil afterDelay:0.0];
```
如果在接受范围内，允许在下一个runloop执行，可以这么做。xCode5没问题，但据反映，xCode6的话这个不能消除警告。

