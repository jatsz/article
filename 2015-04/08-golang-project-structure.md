Go语言项目源代码结构
======================

### 引言

我们在管理项目源代码结构的时候，最终总是要落实到具体文件和文件夹，还有文件和文件夹得之间关系的描述。比如我们传统的C项目，我们使用文件来存储源代码和头文件，用文件夹来存储不同的模块，最后用Makefile来描述这些文件和文件夹的关系。而我们在Visual Studio中新建项目的时候使用文件和文件夹来描述源代码，用project文件来描述文件间的关系。Visual Studio正是使用了这个项目文件最终编译和链接。像这样的情况还有很多，比如Python中使用文件夹来描述包。

总结起来，我们通常都是使用文件来存储源代码使用文件夹来作为模块的划分，最终使用描述文件来描述文件和文件夹的关系。

Go语言比较强调工程性，所以开始的时候Go使用了`约定`的方式来组织文件和文件夹，这样可以省去了Makefile。只要你符合Go项目的源代码规范，Go自动去约定的目录寻找和编译代码。

对于Go的项目说，主要有两块组成：

- Go语言工作空间
- 项目源代码

### Go语言工作空间
