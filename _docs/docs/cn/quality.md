---
title: 质量体系文档
permalink: /docs/quality.md/
redirect_from: /docs/cn/quality.md
---

[English version](../quality_en.md)

# 质量体系

## 概述

curve质量体系是保障项目可持续维护和推进的核心。curve质量体系包括 **理论体系 和 流程控制** 两个部分。

### 理论体系
curve包括了不同层级的测试，是为了更快速的检验新引入功能和已有功能的正确性。

- 单元测试

  对一个模块、一个函数或者一个类来进行正确性检验的测试工作，单元测试可以帮助我们以最小的代价发现代码中的问题。

- 集成测试

  集成测试是在单元测试的基础上，测试在将所有的软件单元按照概要设计规格说明的要求组装成模块、子系统或系统的过程中各部分工作是否达到或实现相应技术指标及要求的活动。

  集成测试的目标是按照设计要求使用那些通过单元测试的构件来构造程序结构。单个模块具有高质量但不足以保证整个系统的质量。有许多隐蔽的失效是高质量模块间发生非预期交互而产生的。

  集成测试是确保各单元组合在一起后能够按既定意图协作运行，并确保增量的行为正确。它所测试的内容包括单元间的接口以及集成后的功能。

- **系统测试**
  系统测试是基于软件需求说明书的黑盒测试，是对已经集成好的软件系统进行彻底的测试，以验证软件系统的正确性和性能等满足其规约所指定的要求，检查软件的行为和输出是否正确，并非一项简单的任务，被称为测试的“先知者问题”。因此，系统测试应该按照测试计划进行，其输入、输出和其他的动态运行行为应该与软件规约进行对比。软件系统测试的方法很多，主要有功能测试，性能测试，随机测试等。

  <img src="/assets/img/images/quality-theory.png" alt="quality-theory.png" width="650" />

  ### 流程控制
  通过严格的流程控制合入仓库的代码质量，保证合入的每一行代码在提交QA前不会影响主流程的功能，并且符合代码规范（curve 使用 c++ 开发，代码规范使用 ```cpplint``` 和 ``cppcheck`` 检查）。后面 curve 的开发会慢慢过渡到 github 上，这套流程也会移植过去。

  这里强调两点： **人工review 和 CI**

  <img src="/assets/img/images/quality-control-1.png" alt="quality-control-1.png" width="850" />

  <img src="/assets/img/images/quality-control-2.png" alt="quality-control-2.png" width="850" />

## 理论体系

### 单元测试

单元测试是用来对一个模块、一个函数或者一个类来进行正确性检验的测试工作，单元测试可以极大程度地保证该模块行为仍然是正确的。这个是软件开发的过程中最基本的测试。可以通过 **代码覆盖率** 去衡量单元测试的完备程度。下面是 curve 代码覆盖率的统计图，主要使用工具   ``lcov(针对c++)  ``统计代码覆盖率，使用 ``genhtml`` 根据 ``lcov`` 的结果进行渲染，其他语言如 go 也有自己的覆盖率统计工具。curve各模块的单元测试在 ``curve/test`` 文件夹下。

<img src="/assets/img/images/quality-cov.png" alt="quality-cov.png" width="950" />

### 集成测试

#### 必要性

集成测试需要考虑的问题主要是各模块连接起来后的问题：

- 穿越模块接口的数据是否会丢失；

- 子功能的组合是否可以达到预期的要求；
- 子模块之间是否会相互影响；
- 单个模块的误差积累是否会放大，从而达到不可接受的程度；

因此，单元测试后，有必要进行集成测试，发现并排除在模块连接中可能发生的上述问题，最终构成要求的软件子系统或系统。

#### 测试内容

- **功能测试**

站在使用者的角度，对模块提供的功能进行完备的测试。在做功能测试前需要设计充分的测试用例，考虑各种系统状态和参数输入下模块依然能够正常工作，并返回预期的结果。在这一过程中，需要有一种科学的用例设计方法，依据这种方法可以充分考虑各种输入场景，保证测试功能不被遗漏，同时能够以尽可能少的用例和执行步骤完成测试过程。

- **异常测试**
  异常测试是有别于功能测试和性能测试又一种测试类型，通过异常测试，可以发现由于系统异常、依赖服务异常、应用本身异常等原因引起的性能瓶颈，提高系统的稳定性。常见的异常如：磁盘错误、网络错误、数据出错、程序重启等等。一般开发阶段的异常我们使用软件模拟，如 c++ 中有 ``libfiu``， go 中的 ``gofail``， 可以模拟软件中的各种异常。

- **规模测试**
  测试模块在一定规模下是否能够正常工作，是否会出现异常或者崩溃，观察系统资源的使用情况。例如测试打开大量的文件是否会出错，内存占用是否会过大。

- **并发/压力测试**

  功能性的测试更多是单线程的测试，还需要在并发场景下对模块进行测试，观察在并发场景下是否能够正常工作，逻辑或者数据是否会出现错误。考虑并发度时，使用较大的压力来测试，例如平时使用的2倍、10倍或更大的压力。

#### 用例设计

以 curve 的 datastore 模块为例，具体的设计方法：[datastore 用例设计](quality-integration-example.md)

### 系统测试

开篇有介绍过，系统测试是黑盒测试。CURVE的系统测试一般是由QA来完成，包含：常规测试、性能测试、稳定性测试、异常测试、混沌测试。

- 常规测试主要是新增功能的手工测试
- 性能测试
- 稳定性测试需要在正常压力下跑足一定的时间
- 异常测试是在正常流程中注入一种软硬件异常
- 混沌测试是在正常流程中随机组合软硬件异常

单元测试和集成测试一般是开发人员在代码开发中编写的，可以不断的积累。在系统测试过程中，我们也希望能做到尽可能所有的用例自动化以降低手工回归成本，使得用例也可以持续积累。

还有很重要的一点：很多隐藏问题都是偶现的，手工测试无法做到频繁触发。

当前curve的 **异常测试以及混沌测试** 实现了自动化。

#### 异常自动化测试实践

效果如下:

<img src="/assets/img/images/quality-auto-abnormal.png" alt=" quality-auto-abnormal.png" width="950" />

#### 框架选型——Robotframework

- 支持python关键字，灵活定义测试
- 完善的测试报告
- 完美兼容Jenkins ci
- 丰富的第三方库（ssh,paramiko,request,multiprocess）
- 参数多样化，支持log level定义，tag筛选，随机打乱等各种批跑模式

<img src="/assets/img/images/quality-auto-robotframework.png" alt="quality-auto-robotframework.png" width="550" />

#### 用例设计原则

- 无需绑定特定环境，“随意拉起”
- 配置化（环境定义配置化、workspace路径、测试负载定义）
- Case独立性
- Case通用性（curve ceph nbs lvm）
- Tag规范(优先级、版本、运行时间)
- 考虑如何提高覆盖率（场景丰富、打乱顺序、某些随机sleep）
- 轮询代替sleep
- 精确性（checkpoint）
- 稳定性（避免环境因素、其他模块干扰）
- 控制用例时间（考虑一些折中方案）

#### 大压力多级故障

大压力多级故障就是curve的混沌测试，也是基于robotframework，但是故障做随机选取后组合注入。

## 流程控制
开篇有介绍，CURVE的流程控制主要是严格的代码合入和 定时的ci触发。

严格的代码依赖于两部分：

1. code review。这个使用的是gerrit平台，两个code review+1，ci+1代码才能合入。code reveiw是代码提交中非常依赖于人工的环节，但是这一环境也很重要，对代码的可读性会有比较高的要求。
2. CI。这个主要对接的是jekins，也得益于异常测试的自动化，代码合入之前需要跑过单元测试、集成测试、异常测试，很大程度上减少了低级bug的出现。

定时的ci触发：

自动化后测试就变得很方便，频繁的触发可以帮助我们发现很多的corner case

<img src="/assets/img/images/quality-process.png" alt=" quality-process.png" width="850" />
