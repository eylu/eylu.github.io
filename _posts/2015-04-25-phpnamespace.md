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
}

/webpath/bpi/Tool.php 文件

class Tool{
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
}

/webpath/bpi/Tool.php 文件

namespace Bpi;
class Tool{
}

/webpath/test.php 文件

require "./api/tool.php";
require "./bpi/tool.php";
```

