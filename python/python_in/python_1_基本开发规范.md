#### 基本规范



> 编码

- UTF-8 编码
- 文件头部添加`#-*-coding:utf-8-*-`

> 代码格式

- 缩进统一使用4个空格缩进
- 自然语言使用双引号`"..."`,错误信息若是unicode,使用`u'你好世界'`
- 机器标识使用单引号`'...'`,如`dict`里的key
- 正则表达式使用原生的双引号`r"..."`
- 文档字符串(docstring)使用三个双引号`"""......"""`

> 空行

- 模块级函数和类定义之间空两行

- 类成员函数之间空一行

  ```python
  class A:
  
      def __init__(self):
          pass
  
      def hello(self):
          pass
  
  
  def main():
      pass 
  ```

> 导包

- import语句应该分行书写

- import语句应该使用 **absolute** import

- 导入其他模块的类定义时,可以使用相对导入

- 如果发生命名冲突,则可以使用命名空间

  ```python
  import os
  import sys
  
  from subprocess import Popen, PIPE
  
  from foo.bar import Bar
  
  import bar
  import foo.bar
  
  bar.Bar()
  foo.bar.Bar()
  ```

> 空格

- 在二元运算符两边各空一个 `[=,-,+,+= -=,>,in,is not,and]`

- 函数的参数列表中，`,`之后要有空格

- 函数的参数列表中,默认值等号两边不要添加空格

  ```python
  def complex(real, imag=0.0):
      pass
  ```

> 命名规范

- 模块尽量使用小写命名,首字母保持小写,尽量不要用下划线
- 类明天使用驼峰命名方式,私有类可用一个下划线开头
- 函数名一律小写,若多个单词使用下划线分割,私有函数在函数前加一个下划线
- 变量名小写,多单词用下划线分割
- 常量全部采用大写