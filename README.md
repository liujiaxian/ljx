### vs code 运行python3.5

#### 环境搭建
1. [VScode下载地址](https://code.visualstudio.com/)
2. [VScode的github项目地址](https://github.com/microsoft/vscode)
3. [Python下载地址](https://www.python.org/downloads/)

#### VScode项目结构简介
> VScode使用的是文件夹命名的项目，也就是说你想写程序的话，需要新建一个文件夹作为你的项目，这个文件夹下放你的源文件，如果需要运行，还需要在这个文件夹下新建.vscode文件夹，在.vscode文件夹下配置这个项目如何运行。

#### 安装Python插件
> 安装Python插件能实现语法提示的一些功能，建议还是安装一下。

打开VScode，查看-->命令面板(Ctrl+Shit+P)，输入ext install （中文输入：扩展，然后选择扩展：安装扩展），在出现的搜索结果中选择找到Python，点右边的那一朵小云就可以安装了。

#### 新建项目和编辑源代码
> 首先在你的电脑里必须有项目文件夹，没有的话新建个，然后点击VScode里的资源管理器按钮，点击蓝色的打开文件夹按钮

新建个python文件

```python
# -*- coding: UTF-8 -*- 
#python3以后
print ("Hello,World!")
print ("你好，世界！")
#python2
#print "Hello,World!"
#print "你好，世界！"
```
#### 编辑task.json任务文件并运行该程序
> 查看-->命令面板(Ctrl+Shit+P)，输入Tasks: Configure Task Runner（中文输入：任务，然后选择任务：配置任务运行程序），选择Other

此时VScode会自动生成.vscode文件夹并生成一个默认的task.json

将task.json内容改为如下内容并保存

```
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "0.1.0",
    //"command": "tsc",
    "command":"python",
    "isShellCommand": true,
    //"args": ["-p", "."],
    "args": ["beautiful.py"],//文件名称
    //"showOutput": "silent",
    "showOutput": "always",
    "problemMatcher": "$tsc"
}
```

#### 运行方法如下：
查看-->命令面板(Ctrl+Shit+P)，输入Tasks: Run Build Task（中文输入：任务，然后选择 任务：运行生成任务(Ctrl+Shit+B)）

> 运行快捷键 ctrl+shift+b
