# Package 包

将模块以文件夹的形式进行分组管理

与文件夹区别：必须包含`__init__.py`文件，在包的根目录存在。在加载包时被调用

![Python程序结构](images\structure.png)



包中`__init__.py` 文件作用

- 一般保存包的文档字符串，描述包的作用
- 存放`__all__`列表
- 导入其他模块



## 导入语法

```python
import 包
import 包.模块
import 包.模块.成员
import 包.子包

from 包 import 模块
from 包.子包.模块 import 成员

from 包 import *
```



## 包内all列表

包内的 `__init__.py` 内的 `__all__` 列表用来记录此包中含有哪些子包或模块可以被 `from 包 import * ` 导入

`__all__`列表限定导入的作用只对 `from 包 import * ` 起作用

