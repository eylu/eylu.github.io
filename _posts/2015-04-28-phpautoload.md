---
layout: default
title: PHP AutoLoad
date: 2015-4-28 10:59:47
category: php
---

# PHP 自动加载 AutoLoad

做 php 项目时，把功能模块化之后，清晰怡人。但是，每次使用模块功能时，总要在文件开头 require 或 include 一些文件，总感觉这样不太好，一定会有办法解决这个问题的。

## \_\_autoload() 与 spl\_autoload\_register

php5 加入自动加载 [\__autoload()](http://php.net/manual/zh/language.oop5.autoload.php) 方法，它会在试图使用尚未被定义的类时自动调用。通过调用此函数，脚本引擎在 PHP 出错失败前有了最后一个机会加载所需的类。但是不建议使用，因为只能定义一次。

[spl\_autoload\_register()](http://php.net/manual/zh/function.spl-autoload-register.php) 将函数注册到SPL __autoload函数队列中。如果该队列中的函数尚未激活，则激活它们。

## spl_autoload_register 介绍

```
spl_autoload_register($function_name, bool $throw = true, bool $prepend = false)
```

$function_name 方法名称: 欲注册的自动装载函数。如果没有提供任何参数，则自动注册 autoload 的默认实现函数spl\_autoload()。

$throw 是否抛出异常: 此参数设置了 $function\_name 无法成功注册时， spl\_autoload\_register()是否抛出异常。

$prepend 是否加入队列之首: 如果是 true，spl\_autoload\_register() 会添加函数到队列之首，而不是队列尾部。

$throw $prepend 作为可选参数，是可以省略的。

## spl\_autoload\_register 示例1

```
/webpath/classa.php 文件
class ClassA{
  public  function __construct(){
    echo "ClassA load success!";
  }
}

/webpath/classb.php 文件
class ClassB extends ClassA {
  public function __construct(){
    echo "ClassB load success!";
  }
}

/webpath/test.php 测试文件
require './classa.php';
require './classb.php';

$a = new ClassA(); // 输出正常
$b = new ClassB(); // 输出正常
```

现在，可以把测试文件开头的 require 去掉，使用 spl\_autoload\_register 函数代替。

```
/webpath/test.php 测试文件

function myload($classname){
  $file = "./$classname.php";
  if(file_exists($file)){
    require $file;
  }else{
    echo "File [$file] not found!";
  }
}

spl_autoload_register('myload');

$a = new ClassA(); // 这里的类名要与文件名相同，输出正常
$b = new ClassB(); // 这里的类名要与文件名相同，输出正常
```

## spl\_autoload\_register 示例2

这次，我们把 spl\_autoload\_register 提到另外的文件去，让每个文件的功能更独立。

在这个例子中，我们有动物类，并且有小猫和小狗两种，他们分别可以喊叫，小猫发出 [miao~ miao~] 声，小狗发出 [wao~ wao~] 声，并且小狗可以学习小猫的 [miao~ miao~] 叫声。

```
/webpath/autoload.php 文件

class AutoLoadClass{
  public static function autoload($classname){
    $file = "./$classname.class.php";
    if(file_exists($file)){
      require $file;
    }
  }
}
spl_autoload_register(['AutoLoadClass','autoload'],true,true);
```

```
/webpath/animal.php 文件，动物类，包含属性 speak，发声的方法 say()

require 'autoload.php';

class Animal{
  public $speak;

  public function say(){
    $me = get_called_class();
    echo " $me speak [ $this->speak ]";
  }
}

$dog = new Dog();
$dog->say(); // 输出 'wao~ wao~'
$dog->learn(); // 通过学习，输出 'miao~ miao~'
```

```
/webpath/cat.class.php 文件，Cat类，内置属性 speak 值 'miao~ miao~'

class Cat extends Animal{
  public $speak = 'miao~ miao~';
}
```

```
/webpath/dog.class.php 文件，Dog类，内置属性 speak 值 'wao~ wao~',
并且有自己的“学习”方法。

class Dog extends Animal{
  public $speak = 'wao~ wao~';

  public function learn(){
    $cat = new Cat();
    $cat->say();
  }
}
```
