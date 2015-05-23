---
layout: default
title: AngularJs（二） 数据初始化、迭代器
date: 2015-05-09 11:22:00
category: JS
---

上一篇已经对 Angularjs 有一个初步的认识，接下来我们会逐步的深入了解 AngularJs ，并以一些示例做简单说明。

## 示例：数据初始化 ng-init

我们通过几个示例来看看数据如何初始化，并且，初始化之后，我们能做什么。

```
<!DOCTYPE html>
<html ng-app>
  <head>
    <meta charset="utf-8">
    <title>AngularJs 示例: ng-init</title>
    <script type="text/javascript" src="/path/to/angular.js"></script>
  </head>
  <body>
    <pre>ng-init="name='David';checked=true;"</pre>
    <div ng-init="name='David';checked=true;">
      <input type="text" ng-model="name" /> Your name is: {{name}}
      <hr>
      <input type="checkbox" ng-model="checked" /> {{checked}}
    </div>
  </body>
</html>
```

上例代码中我们初始化了两个模型变量 name 和 checked，当有dom绑定数据变量时，就会使用模型变量的值作为默认值。

`ng-init` ：初始化一些模型变量


## 示例：迭代器 ng-repeat

```
<!DOCTYPE html>
<html ng-app>
  <head>
    <meta charset="utf-8">
    <title>AngularJs 示例: ng-repeat</title>
    <script type="text/javascript" src="./src/Angular-1.4.0-rc2.js"></script>
  </head>
  <body>
    <pre>ng-init="list = ['Chrome', 'Safari', 'Firefox', 'IE'] "</pre>
    <div ng-init="list = ['Chrome', 'Safari', 'Firefox', 'IE'] ">
      <input ng-model="list" ng-list> <br>
      <pre>list={{list}}</pre>
      <ol>
        <li ng-repeat="item in list">
          {{item}}
        </li>
      </ol>
    </div>
  </body>
</html>
```

上例代码中我们初始化了一个模型变量 list ，然后我们使用 ng-repeat 迭代 list 的值，并显示到页面中。

`ng-repeat="item in list"` ：迭代 list 并声明每项为 item 。


我们循环迭代了数组，如果 list 不是数组，是一个 Object ，我们改怎么办呢？ 如下：

```
<!DOCTYPE html>
<html ng-app>
  <head>
    <meta charset="utf-8">
    <title>AngularJs 示例: ng-repeat</title>
    <script type="text/javascript" src="./src/Angular-1.4.0-rc2.js"></script>
  </head>
  <body>
    <pre>ng-init="list = [{name:'David',age:18},{name:'Lucy',age:20},{name:'Lilei',age:19}] "</pre>
    <div ng-init="list = [{name:'David',age:18},{name:'Lucy',age:20},{name:'Lilei',age:19}] ">
      <input ng-model="list" ng-list> <br>
      <pre>list={{list}}</pre>
      <ol>
        <li ng-repeat="person in list">
          {{person.name}}'s age is: {{person.age}}
        </li>
      </ol>
    </div>
  </body>
</html>
```

上例代码中，我们初始化 list ，每项是一个 Object ，每个 Object 有两个属性 name 和 age ，我们循环输出每个 Object 的属性值。

`{{person.name}}` ：调用属性，显示属性值。


参考资料：
[http://angularjs.cn/](http://angularjs.cn/)
[http://docs.angularjs.cn/](http://docs.angularjs.cn/)
[http://docs.angularjs.cn/api](http://docs.angularjs.cn/api)