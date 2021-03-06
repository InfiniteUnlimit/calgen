# calgen

> 17级计科软件工程结对项目

[TOC]

## 分析与设计


主流程: 参数处理 => 生成题目 => 主循环 (用户交互, 做题判题) => 打印分数

### 参数处理

字符串处理问题

说明: 只接受 `-n` 参数, `-n` 之后的数值有大小范围

解决: 函数 (*牺牲可扩展性, 但是编写容易*)

函数描述:

```C++
// argc, argv是来自命令行的参数信息
// 返回值: 解析出来的 -n 之后的数值
// 异常: 
//   1. 没有参数, 返回一个默认值, 例如5
//   2. 设定取值范围, 如果超出范围, 打印该程序使用说明并退出程序 (exit(0))
//	 3. 如果有额外的字符或格式错误, 比如 -n 5 x, 打印该程序使用说明并退出程序 (exit(0))
// 使用说明打印格式参考 unix 命令说明
int extractPara(int argc, char ** argv);
```

### 生成题目

生成题目, 放入 `vector` 中

#### Random类

用于生成随机数, 已经写好, 用法见源文件

#### Number类

生成的题目包含分数运算, 因此Number类是一个支持分数运算的数值类.

需要重载运算符, 和输入输出流, 从而使其能够像 `int` 等内置类型一样使用

#### RandomQuestion类

随机生成题目, 以`string`保存题面, 以`Number`保存结果

```C++
成员数据:
	private string
    private Number
成员函数:
	public RandomQuestion() // 构造函数, 随机生成题目
    public const string& getQuestion() // 返回题面
    public const Number& getAnswer() // 返回答案
```

#### 随机生成方案

可选方案: 

1. 随机生成后缀表达式较容易, 然后转换成带括号的中缀表达式
2. 使用随机数构造表达式二叉树

先考虑方案二: 如何从二叉树返回一个代表中缀表达式的字符串

当 `+` 或 `-` 的父节点是 `*` 或 `÷` 的时候需要加括号..? 测试

然后测试时发现当一个节点为`*`或`÷`并且它的右子节点也是`*`或`÷`时, 右子节点的串也需要加括号.

!! 想了半天, 原来bug出在Random

优化后的方案(最终方案):

当前节点优先级大于左子节点运算符优先级时, 左子树需要加括号

只要右子节点不为数字, 而且右子节点优先级不大于当前节点, 右子树就需要加括号

### 用户交互

可以直接写在主函数内.

遍历生成的题目的 `vector<RandomQuestion>`, 打印题目, 等待用户输入, 比较, 反馈正误, 打印下一题.

记录总得分, 最后反馈正确率(打印分数).

## 代码规范

### 代码风格规范

1. **缩进与空行**
   1. 缩进统一使用4个空格, 而不使用tab
   2. 左右花括号都要独占一行
   3. `if`  `while`  `for` 等之后的代码块需要有 `{}` (特指只有一条语句的时候)
   4. 一条语句独占一行
2. **命名**
   1. 变量名采用小驼峰法, 尽量是名词性或形容词性
   2. 方法名采用小驼峰法, 尽量使动词或动宾结构
   3. 类名采用大驼峰法, 尽量使名词性, 次之形容词性
   4. 尽量不要使用下划线
3. **注释**
   1. 类和方法必须要有注释, 说明它们的用途

### 代码设计规范

1. 单一职责: 类/函数都应该只做一件事
2. 异常处理: 预测可能的异常, 使用 `try...catch`
3. 尽量不要使用继承/多态
4. 将头文件和源文件分离, `.h`声明, `.cpp` 定义
