> 本文记录一下在做项目时总结的一些零碎知识点，纯粹是个人的笔记记录，并书中所谓的专业的项目开发的流程或事项，想到什么就写什么，呵呵。




## 需求分析

* 需要做一个什么样的东西
* 为什么需要这个东西
* 此项目需要具有什么样的功能

## 设计阶段
* 设计整体系统架构
* 设计整体业务逻辑流程
* 设计各个功能模块
* 设计函数接口定义

对外输出需要有：
1. 整体架构图
2. 业务流程图
3. 各个功能模块的要实现的功能及实现方式
4. 函数接口的定义的文档

设计阶段的每一块都需要团队内大量的讨论，确定方案后最好能不修改或少的修改。


## 开发阶段
* 最好严格按照设计文档来开发
* 所写代码的风格要符合规范标准
* 实现逻辑最好简单易读
* 变量和函数名称"见名知意"
* 要注意代码的复用性
* 时刻谨记对异常的处理
* 输入参数一定要做相关检查和校验
* 编码与测试同步进行
* 尽可能的不使用第三方库
* 接口定义完毕尽可能不要改动
* 减少项目对系统环境的强依赖
* 注释需要能够说明 WHY 而不是 HOW
* 功能函数对参数和返回值要有注释说明
* 代码版本管理用 gitlab 或 github


## code review
code review 是个非常重要的流程，但是国内大部分公司是不怎么去做代码的 review 的，即使一些公司做 review ，也可能只是形式上的，没有达到 review 的真正目的。

code review 不是非要等到所有代码都开发完成后，才做 review 的，这样的 review 是不好的，因为：

1. 代码量很大程度上增加了 review 的难度
2. 在 review 过程中能够发现的问题可能会很多，很难修改
3. 有些属于设计模式或接口性质的问题，应该在早期发现并修改
4. 后期发现的问题有可能会引起其它的问题，引起连锁反应
5. 代码作者被发现出很多问题时，大都可能是有抵抗情绪的

个人感觉应该是这样：

1. 增加 code review 的次数，每 1-2 周做一次 review
2. 开发前期的设计模式及函数接口设计阶段也要参与 code review，以便能够及时发现问题
3. 要提前制定本项目 review 的 checklist
4. 交叉 review，认真听取别人提的问题和建议

具体内容见另一篇文章：《[软件开发之code review](https://www.linuxblogs.cn/articles/dev-code-review.html)》


## 测试阶段
* 测试 case
* 单元测试
* 功能测试
* 正常测试和异常测试
* 测试团队来做测试


## 上线前的准备
最为重要的是对上线前的一些评估：

* 考虑对运维人员的变化，运维该如何执行日常运维工作
* 与运维人员的相关交接：使用培训、文档说明、错误处理等
* 考虑实际业务人员的使用：使用文档
* 考虑与值班人员的交接：告警、故障处理


## 上线
* 上线环境部署 -> 形成文档
* 上线流程步骤 -> 形成文档
* 执行回滚过程 -> 形成文档


## 后期维护
* 相关文档的维护：设计文档、开发文档、运维文档
* 监控、告警、常见故障处理流程
