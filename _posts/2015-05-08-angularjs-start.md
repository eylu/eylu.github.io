---
layout: default
title: AngularJs（一） 初体验
date: 2015-05-08 15:38:00
category: JS
---

# AngularJs 介绍

AngularJS是为了克服HTML在构建应用上的不足而设计的。HTML是一门很好的为静态文本展示设计的声明式语言，但要构建WEB应用的话它就显得乏力了。所以我做了一些工作（你也可以觉得是小花招）来让浏览器做我想要的事。

通常，我们是通过以下技术来解决静态网页技术在构建动态应用上的不足：

类库 -

  类库是一些函数的集合，它能帮助你写WEB应用。起主导作用的是你的代码，由你来决定何时使用类库。类库有：jQuery等

框架 -

  框架是一种特殊的、已经实现了的WEB应用，你只需要对它填充具体的业务逻辑。这里框架是起主导作用的，由它来根据具体的应用逻辑来调用你的代码。框架有：knockout、sproutcore等。

AngularJS使用了不同的方法，它尝试去补足HTML本身在构建应用方面的缺陷。AngularJS通过使用我们称为标识符(directives)的结构，让浏览器能够识别新的语法。例如：

  使用双大括号{{}}语法进行数据绑定；

  使用DOM控制结构来实现迭代或者隐藏DOM片段；

  支持表单和表单的验证；

  能将逻辑代码关联到相关的DOM元素上；

  能将HTML分组成可重用的组件。

端对端的解决方案

  AngularJS试图成为WEB应用中的一种端对端的解决方案。这意味着它不只是你的WEB应用中的一个小部分，而是一个完整的端对端的解决方案。这会让AngularJS在构建一个CRUD（增加Create、查询Retrieve、更新Update、删除Delete）的应用时显得很“固执”（原文为 opinionated,意指没有太多的其他方式）。但是，尽管它很“固执”，它仍然能确保它的“固执”只是在你构建应用的起点，并且你仍能灵活变动。AngularJS的一些出众之处如下：
  构建一个CRUD应用可能用到的全部内容包括：数据绑定、基本模板标识符、表单验证、路由、深度链接、组件重用、依赖注入。
  测试方面包括：单元测试、端对端测试、模拟和自动化测试框架。
  具有目录布局和测试脚本的种子应用作为起点。

AngularJS的可爱之处

  AngularJS通过为开发者呈现一个更高层次的抽象来简化应用的开发。如同其他的抽象技术一样，这也会损失一部分灵活性。换句话说，并不是所有的应用都适合用AngularJS来做。AngularJS主要考虑的是构建CRUD应用。幸运的是，至少90%的WEB应用都是CRUD应用。但是要了解什么适合用AngularJS构建，就得了解什么不适合用AngularJS构建。
  如游戏，图形界面编辑器，这种DOM操作很频繁也很复杂的应用，和CRUD应用就有很大的不同，它们不适合用AngularJS来构建。像这种情况用一些更轻量、简单的技术如jQuery可能会更好。

# AngularJs 示例

现在，我们看两个最简单的 AngularJs 示例，hello world 和 双向数据绑定

## 示例：Hello World

```
<!DOCTYPE html>
<html ng-app>
  <head>
    <meta charset="utf-8">
    <title>AngularJs 示例:Hello World</title>
    <script type="text/javascript" src="/path/to/angular.js"></script>
  </head>
  <body>
    <p>Hello {{'world'}}</p>
  </body>
</html>
```

上例代码与普通 html 页面的主要区别：

`<html ng-app>` ：当加载该页时，标记 ng-app 告诉 AngularJS 处理整个HTML页并引导应用；

```{{'world'}}``` ：使用双大括号标记 {{}} 的内容是问候语中绑定的表达式，这个表达式是一个简单的字符串 ‘world’。

## 示例：双向数据绑定 ng-model

```
<!DOCTYPE html>
<html ng-app>
  <head>
    <meta charset="utf-8">
    <title>AngularJs 示例:双向数据绑定 ng-model</title>
    <script type="text/javascript" src="/path/to/angular.js"></script>
  </head>
  <body>
    Your name: <input type="text" ng-model="yourname" placeholder="World">
    <hr>
    Hello {{yourname || 'world'}}!
  </body>
</html>
```

我们来看看这例代码与普通 html 页面的主要区别：

`<html ng-app>` ：当加载该页时，标记 ng-app 告诉 AngularJS 处理整个HTML页并引导应用；

`ng-model="yourname"` ：文本输入指令 `<input ng-model="yourname" />` 绑定到一个叫 yourname 的模型变量；

`{{yourname || 'world'}}` ：使用双大括号标记 {{}} 的内容是问候语中绑定的表达式，这个表达式是一个模型变量,如果没有值时，默认值是字符串 ‘world’。

我们在文本域 `<input />` 中输入时，下面的 {{yourname}} 也会随之改变，不用自己去写监听事件，处理事件。



参考资料：
[http://angularjs.cn/](http://angularjs.cn/)
[http://docs.angularjs.cn/](http://docs.angularjs.cn/)
[http://docs.angularjs.cn/api](http://docs.angularjs.cn/api)