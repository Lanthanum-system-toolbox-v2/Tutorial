# “机型修改”模块编写教程
### 这将提供编写一个可以被加载到“机型修改”中的机型模块编写教程
- 首先创建一个空文本文件，命名随意（按官方标准为device_xxxx)
- 写入机型模块头<code>!device</code>
- 写入显示的名称<code>name=测试机型</code>
- 以下是其他你可以定义的机型信息参数，将他们作为新的行写入文本文件
- <code>ro.product.model=xxxx</code>
- <code>ro.product.brand=xxxx</code>
- <code>ro.product.manufacturer=xxxx</code>
- 以下是不推荐作为修改内容的部分，因为他们与大多数应用的机型检测无关，却与许多ROM能否正常工作有一定关联。
- <code>ro.build.product=xxxx</code>
- <code>ro.product.device=xxxx</code>
- 定义完毕后 写入<code>!device</code>作为结尾
- 然后 可以使用 加载第三方模块 进行测试运行
- [Demo](https://github.com/xzr467706992/Lanthanum_system_toolbox_v2/tree/device_list)
