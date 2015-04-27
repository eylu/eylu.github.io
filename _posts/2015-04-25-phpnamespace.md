---
layout: default
title: PHP NameSpace
date: 2015-4-25 09:26:47
category: php
---

# 命名空间 NameSpace

类名定义时遇到冲突，可以使用命名空间来解决

### 示例

设 webpath 为项目目录，此时有两个组件(Api、Bpi)中都有类 Tool，如果直接声明 class Tool{} ，则会报错 Fatal error: Cannot redeclare class Tool

```
/webpath/api/Tool.php 文件

class Tool{
  function hello(){
    echo "api file, Tool hello()";
  }
}

/webpath/bpi/Tool.php 文件

class Tool{
  function hello(){
    echo "bpi file, Tool hello()";
  }
}

/webpath/test.php 文件

require "./api/tool.php";
require "./bpi/tool.php";
```

如果使用 namespace 将其分隔开，就不会再报错了。

```
/webpath/api/Tool.php 文件

namespace Api;
class Tool{
  function hello(){
    echo "<br>api file, Tool hello()<br>";
  }
}

/webpath/bpi/Tool.php 文件

namespace Bpi;
class Tool{
  function hello(){
    echo "<br>bpi file, Tool hello()<br>";
  }
}

/webpath/test.php 文件

require "./api/tool.php";
require "./bpi/tool.php";

$tool_a = new Api\Tool();
$tool_b = new Bpi\Tool();

$tool_a->hello(); // 输出 api file, Tool hello()
$tool_b->hello(); // 输出 bpi file, Tool hello()
```

而且，我们通常把文件夹的名字或者目录名作为 namespace 的名称，这样更有助于我们查看和管理。

有时候，我们会使用 use..as 给命名空间起一个简短的别名。

```
/webpath/api/db/mysql.php 文件

namespace Api\Db;
class Mysql{
  function name($str = ''){
    echo "<br>Class Api\Db\Mysql name() -> Hello $str !<br>";
  }
}

/webpath/test2.php 文件

require "./api/db/mysql.php";

use Api\Db\Mysql;
use Api\Db as AD;
use Api\Db\Mysql as ADM;

$db = new Api\Db\Mysql();
$db2 = new AD\Mysql();
$db3 = new ADM();

echo $db->name('world');  // 输出 Class Api\Db\Mysql name() -> Hello world !
echo $db2->name('world 2');  // 输出 Class Api\Db\Mysql name() -> Hello world 2!
echo $db3->name('world 3')  // 输出 Class Api\Db\Mysql name() -> Hello world 3!
```