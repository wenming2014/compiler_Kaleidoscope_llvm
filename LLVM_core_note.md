#<LLVM编译器实战教程>阅读笔记
目的:前段时间搞了下TVM,里面和llvm相关,就了解下,只是作为一个概述性的介绍,后序需要再接着深入研究
llvm综述
- 是什么
编译器框架,编译器本质是将一种高级语言翻译成低级语言
- 作用
链接软件(程序)和硬件
- 优点
易于扩展,统一的IR表示,设计成多个库(模块化)可以提高复用并且使得代码结构清晰,使得编译器开发学习所涉及的过程变得清晰,而gcc没有很好解耦对于要支持新的语言和新的硬件会很麻烦,LLVM的升级版NNVM和relay
静态编译和动态编译,jit
应用:新语言支持,新的后端支持
扩展:任务调度器,GPU加速器
------
- ==内容==
    * 前端:词法,语法,语义,IR生成
    * ==IR:使用AST在内存中表示程序==,可存在内存和磁盘中,磁盘中的形式.bc(二进制)和.ll(人类可读的汇编)
    * ==后端:得到汇编或者目标代码,包含指令选择和寄存器分配,将程序转化为DAG以便指令选择,然后转化为三地址码表示,从而进行指定调度,最后再实现汇编和链接==
    * JIT引擎
    * 交叉编译
    * 插件接口:代码规范,代码优化
-------
- LLVM适合哪些人
    对C和C ++程序的编译时，链接时（过程间）和运行时转换感兴趣的编译器研究人员；
    对可移植的，与语言无关的指令集和编译框架感兴趣的虚拟机研究人员
    对编译器/硬件技术感兴趣的架构研究员
    对静态分析或插桩技术感兴趣的安全研究人员
    想要快速开发编译器原型的教师或开发人员
    希望获得更好性能的最终用户开发者

---
##chapter 1 构建和安装LLVM
---
##chapter 2 外部项目
###clang
==LLVM=LLVM core+clang
clang:前端,负责将c/c++转化为IR==
###DragonEgg插件
clang只能处理c/c++,gcc可以处理其他的语言,所以将gcc作为LLVM的前端,使得LLVM支持更多的语言

---
##chapter 3 工具和设计
- 设计思想
==LLVM由库和工具(也是由库构成)做成,通过提供可插拔的接口从而允许在整个编译过程中方便的集成转化和优化步骤,且可以自由组合及调换顺序,opt,llc,llvm-as...
一切都是库
用于指导核心库开发的两个LLVM IR的基本原则:==
    * SSA表示很允许你快速优化的无线的寄存器
    * 通过将整个程序存储在磁盘的IR表示,以实现便捷的俩链接时优化
LLVM组件之间的交互通过内存或者文件
clang就是编译器的驱动程序


- SSA:静态单赋值特点:每个变量只有唯一定义它的赋值语句,并且不能被重新赋值
==代码被组织为三地址指令:操作码有俩个操作数
数目不受限制的寄存器:%对应的局部变量有无穷多个==

==llvm的c++惯例
源码阅读建议==

---
##chapter 4 前端
##chapter 5 IR
IR是在高级语言和硬件之间的折中,并且能够目标无关(不同后端能够共享对程序的理解),并且比java字节码更低层次

---

##chapter 6 后端
==由代码生成器和pass组成==
---

后端过程
- 指令选择
将IR转化为指定目标的SelectionDAG节点,这些节点组成DAG,每个节点就是一个目标机器指令,通过模式匹配的指令选择算法
- 指令调度
DAG北京包含指令间的依赖顺序,所以通过指令调度(前寄存器分配调度),带到指令集并行并排序,得到MachineInstr的三地址表示形式
- 寄存器分配
将无限寄存器转换为特定目标的有限寄存器集
- 后指定调度
后寄存器分配调度,根君硬件资源的竞争关系和不同呢寄存器的访问延时提升代码质量
- 代码输出(code emission)
将MachineInstr转换为MCInst,用于汇编器和链接器

---

使用tableGen实现LLVM后端
TableGen:

    描述目标机器的信息:指令格式,指令,寄存器,模式匹配DAG,指令选择匹配顺序,调用惯例,cpu属性,ISA
    声明性面向记录的语言,描述编译阶段使用的信息,即.td文件
    X86.td
    RegisterInfo.td
    InstrFormats.td

chapter 7 JIT
chapter 8 跨平台编译
chapter 9 clang静态分析器
无需运行检查错误
chapter 10 


##其他
bitcode(IR中间表示所需的空间)和java字节码不一样
LLVM:a compilation framework for lifelong Program analysis & transformation
可以下代码
