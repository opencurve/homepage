---
layout: post
title:  "开源软件供应链点亮计划 - 暑期 2021"
author: xuchaojie
---

# 开源软件供应链点亮计划 - 暑期 2021 简介

开源软件供应链点亮计划 - 暑期 2021（以下简称 暑期 2021）是由 中国科学院软件研究所 与 openEuler 社区 共同举办的一项面向高校学生的暑期活动，旨在鼓励在校学生积极参与开源软件的开发维护，促进优秀开源软件社区的蓬勃发展。

该计划将联合各大开源社区，针对重要开源软件的开发与维护提供 mini 项目，并向全国高校学生开放报名。学生可自主选择感兴趣的项目进行申请，并在中选后获得该软件资深维护者（社区导师）亲自指导的机会。根据项目的难易程度和完成情况，参与者还可获取“开源软件供应链点亮计划-暑期2021”活动奖金和奖杯。

暑期2021活动官网：https://summer.iscas.ac.cn/#/homepage
Curve项目页面：https://summer.iscas.ac.cn/#/org/orgdetail/opencurve


## 活动的组织方

本次活动由中国科学院软件研究所、openEuler 社区主办，当前是第二届。

## 活动参与方及角色

活动参与方主要角色为学生、社区和导师。

- 学生：学生自由选择项目，与社区导师沟通实现方案并撰写项目计划书。被选中的学生将在社区导师指导下，按计划完成开发工作，并将成果贡献给社区。社区评估学生的完成度，主办方根据评估结果发放资助金额给学生。
- 社区：社区提供项目列表和描述，并安排项目对应的导师，导师与申请者沟通方案、并从申请者中选中一位承接项目。在为期三个月的开发周期中，导师指导学生进行对应项目的开发工作。
- 导师：社区针对每一个项目指定一个社区导师，与学生一起制定合适的开发计划和方案，指导学生按计划完成开发。

## 项目的奖金
- 每个项目难度分为高、中、低三档，对应税前奖金分别为高（12000 元）、中（9000 元）、低（6000 元）。（注：奖金数额为税前人民币金额）。
- 难度分级由社区根据项目任务决定。

# 本次Curve社区参与暑期2021项目介绍

## 1. ChunkServer端client模块增加打开文件缓存超时或淘汰策略

- 项目标题：ChunkServer端client模块增加打开文件缓存超时或淘汰策略
- 项目描述：
Curve ChunkServer端使用的client模块打开curve卷，这个卷并没有关闭的地方，一直在内存中打开着。当卷没有关闭时，client中会一直缓存着Meta Cache等卷的缓存信息，从而占用内存。
这一部分需要做一个超时关闭逻辑，或者是LRU的淘汰策略。

- 项目难度：中等
- 项目社区导师：许超杰
- 导师联系方式：xuchaojie@corp.netease.com
- 项目产出要求：
  + 简要方案设计，可以以issue的方式提至https://github.com/opencurve/curve/issues，需包含几种策略的比较，考虑点说明，为什么采用这种方式。
  + 代码开发，pr的方式提至curve仓库 https://github.com/opencurve/curve/pulls，需符合curve遵循的代码规范，补充单元测试，并单元测试覆盖率通过，CI测试通过，并经过curve项目组code review完成合入。
- 项目技术要求：
  + 熟悉C++软件开发，熟悉gtest等相关单元测试框架，能够完成单元测试等工作。
  + 熟悉常用缓存淘汰策略，如超时淘汰，LRU的淘汰策略，了解各自优缺点。
  + 熟悉linux上的编译，调试命令，如gcc，gdb等。
  + 了解curve的单机部署，能完成相关的功能测试。

- 相关仓库地址：
  + Curve: https://github.com/opencurve/curve
  + Curve meetup slides：https://github.com/opencurve/curve-meetup-slides

## 2. python重构curve_ops_tool

- 项目标题：python重构curve_ops_tool
- 项目描述：
Curve作为一个大型分布式存储系统，在可运维性和可观测性上面都有非常高的要求。开发人员对运维工具的需求可以分为以下几个方面：

**可观测性**

curve是一个底层存储软件，对外提供的client也是一个动态库的方式，没有类似前端之类的展示界面。所以系统内部的各种数据的可观测性是一个很强烈的需求。

集群状态。需要观测到集群的空间信息，服务的健康状态等信息。
命名空间查询。curve以目录树的方式组织系统中的文件，所以需要提供一个查询文件状态，list目录的功能。
复制组健康状态查询。在升级过程中，需要操作一批机器之后确保复制组恢复健康才可以继续操作下一批机器。
三副本一致性检查。

**可运维性**

除了查询类的功能，有时候也需要下发一些运维操作。

文件操作类。有时候需要执行一些文件管控类的操作，比如创建文件，删除文件，清空回收站数据等。
配置变更类。正常情况下的配置变更都是由mds下发的，但是在一些极端场景下，可能需要人为介入下发配置变更。
当前的curve_ops_tool是使用C++开发的，对于命令行的支持，字符串的解析，和结果的输出都不是很友好，在curve_ops_tool功能越来越复杂的时候，扩展性也会越来越差。因此需要使用python重构curve_ops_tool，优化工具使用便捷性和输出美观性以及代码简洁性。

- 项目难度：较难
- 项目社区导师：李小翠
- 导师联系方式：lixiaocui1@corp.netease.com
- 项目产出要求：
  + 简要方案设计，以issue的方式提至https://github.com/opencurve/curve/issues
  + 代码开发，pr的方式提至curve仓库 https://github.com/opencurve/curve/pulls，需符合curve遵循的代码规范，补充单元测试，并单元测试覆盖率通过，CI测试通过，并经过curve项目组code review完成合入。
- 项目技术要求：
  + python/c++等编码能力
  + 深入理解curve_ops_tool的工作原理，了解brpc和proto的使用方式
  + 熟练掌握curve编译方式
- 相关的开源软件仓库列表：
  + Curve: https://github.com/opencurve/curve
  + Curve meetup slides：https://github.com/opencurve/curve-meetup-slides

## 3. curve快照克隆一致性检查工具
- 项目标题：curve快照克隆一致性检查工具。
- 项目描述：
curve发起快照（克隆）之后，会生成一个快照卷（克隆卷）。希望有工具能够检查源卷和快照卷（克隆卷）的数据是否一致。如果不一致，能够找到不一致的地方，直观方便地帮助定位问题。
工具建议使用python开发，要求：

  + 能够比较源卷和克隆卷的不一致的位置
  + 能够比较源卷和快照的不一致的位置
  + 能够比较快照和从快照克隆的卷的不一致的位置
  + 直观地展示不一致的信息，包含不一致的offset，所属chunk(chunk id，chunk所在copyset id，chunk的几个副本所在chunkserver ip和hostname)，不一致的数据等。
- 项目难度：中等
- 项目社区导师：陈威
- 导师联系方式：hzchenwei7@corp.netease.com
- 项目产出要求：
  + 详细方案设计，可以以issue的方式提至https://github.com/opencurve/curve/issues，需包含几种策略的比较，考虑点说明，为什么采用这种方式。
  + 代码开发，pr的方式提至curve仓库 https://github.com/opencurve/curve/pulls，需符合curve遵循的代码规范，补充单元测试，并单元测试覆盖率通过，CI测试通过，并经过curve项目组code review完成合入。
- 项目技术要求：
  + 熟悉C++软件开发，熟悉gtest等相关单元测试框架，能够完成单元测试等工作。
  + 熟悉linux上的编译，调试命令，如gcc，gdb等。
  + 了解curve的单机部署，能完成相关的功能测试。
  + 熟悉Python开发，能够使用python完成工具开发。

- 相关的开源软件仓库列表：
  + Curve: https://github.com/opencurve/curve
  + Curve meetup slides：https://github.com/opencurve/curve-meetup-slides

## 更多项目陆续更新中，敬请期待...






