# 项目生命周期和工作组

## 介绍

此处所定义的生命周期允许:

  * 简单的项目孵化
  * 明确地指导项目直至健康成熟的状态
  * 一个清晰的过程将项目分配给新的或现有的工作小组
  * 一个简单的生命周期,确保活动的项目可以在必要的时候停止使用。

 发布周期提出了成熟的工作组

  * 是简单、轻量级
  * 提供足够的能见度给工作小组 
     相互协调,避免不必要的
     重复的工作。

同时发布过程提出了一个简单的过程协调工作组正式走向
统一、集成,相互操作。

## 定义

### 服务

服务是一个代码体扩展功能核心。服务的例子包括：支持特定于设备的功能（如在电源设置微波或电视上的输入源），或桥梁的提供向下和横向兼容性支持协议如DLNA。

### 项目

一个项目的组织单元产生一个或多个服务,执行特定功能的文本中的AllSeen联盟。项目运作开源模型中定义的联盟,并由一个或多个提交者过目。 Each 每个项目必须只属于一个工作组。

### 工作组

一个工作小组是一个或多个项目的逻辑分组。工作组是一个精英组织,遵循开源生产最佳实践代码范围内设定的政策技术指导委员会和董事会。项目工作组内委托人做出决定和选举工作小组主席作为一个领袖和代表工作小组技术指导委员会。

## 综述

项目是最基本的组织单位内AllSeen的联盟。他们是代码驱动,精英开源项目生产特性和功能设定的政策范围内的技术指导委员会和董事会。每个项目狭义范围应该是合理的，让参与者专注于做一些非常好的事情。每项目应该至少有一个提交者，以确保负责应用代码到主线库的人或人们是熟悉的基本代码的。

工作小组是逻辑分组的项目取得了成熟的状态。在最简单的情况下,新创建的项目将会是自己的工作小组。在创建多个相关项目的情况下,他们可能会被分成相同的工作组。如果一个工作小组不再包含任何成熟的项目,它将被移除。

每个工作小组由贡献者和项目的提交者组成。工作小组将不时选出一个领导者，作为工作组主席。工作小组主席负责监督工作小组的活动,以及代表工作小组和项目技术指导委员会。

## 项目类型和生命周期

### 项目状态

项目的有效状态:

| 项目阶段      | 描述
|----------------|------------------------------------------
|提议           | 不是真实存在的, 没有真的
|               | 资源,但预计将创建一个特定的需求。
| 孵化        	 | 项目有资源，但被认为是在早期阶段，还没有作出贡献的产品准备代码。
| 成熟          | 项目是已分配给一个工作组一个全功能的，成功的开源项目
|存档           | 项目已被确认为终止，并已被封存，因为它不再是一个我们持续关注的内容了。
 
### 项目状态转换

有效转换（以及它们的相关的评价）如下：

|起始状态    | 目的状态   | 审查
|---------------|-------------|-----------------
| &lt;null&gt;  | 提议	  | &nbsp;
| 提议	    |孵化  | 创建评审
| 孵化    | 成熟     | 毕业审查
| {提议, 孵化, 成熟} | 存档| 终止审查

### 审查过程

对于每一个评审,会有一个公开可见的wiki / web模板包含相关评论信息的填写,如下的章节中概述。

评审文档必须张贴和公开宣布审查日期前至少2周的复习计划,给参与者足够的时间来决定。

审查理想情况下应该进行的方式，它对全球性社区反应很敏捷(即,地理位置和时区色散)。

评论可能会在某些情况下进行合并（例如，对于一个项目的创建和结业审核成已经成熟的以前的场所）。如果把审查结合起来，审阅文档只需要发布一个单人的两周回顾期内联盟。

####  创建审查过程和标准

  1. 建议发布2周的审查。
     [方案模板][提案样本] 
     a. 名称(商标)好
     b. 描述完整 
     c. 范围是定义良好的
     d. 资源承诺(开发人员致力于工作)
     e. 提交者识别
     f.满足董事会政策(包括知识产权)
     g. 提出了最初的工作小组
     h. 建议通过电子邮件发送给TSC邮件列表允许2周回顾：allseen-tsc@lists.allseen alliance.org
  2. TSC的审查和批准

#### 通过审核过程和标准

  1. 毕业的建议发布两周:
     a.可以演示代码基础准备的商业产品
     b.有一个活跃的社区
     c. 演示了一个版本(使用成熟的发布过程)
     d. 证明所需的工作小组(新的或现有的)
     e. 接受加入工作组提出的任何条件
  2. 通过审查项目的提交者寻求投票
  3. 接受工作小组投票,如果它已经存在。
     否则,接受的TSC投票来创建一个新的工作小组使用[创建审查过程和标准][creation-process]
[creation-process]: #creation-review-process-and-criteria

  4. TSC的审查和批准


#### 终止审查过程和标准

  1。终止建议发布两周:
    a.寻求州项目终止的原因
    b.估计对其他项目,用户,社区的影响,如何减轻这些影响
    c. 表示该项目将会存档
  2. 可以发起投票委员会的项目
  3. 可以由TSC或工作小组如果有任何剩余项目的提交者和没有SCM在18个月内提交
### 
工作组之间的转换


不时的项目可能有理由改变工作组成员。例子包括:

  * 一个工作组的范围已经随着时间的推移和某些与其他项目不再有明显的协同效应
  * 两个工作组的范围随着时间而演变，且功德整合非常相似

#### 过程改变工作小组成员

  1.工作组发布的更改建议两周
     a. 期望的工作小组
     b. 离开现有的工作小组的原因
     c. 加入新的工作组的原因
  2.在新工作小组审查和批准的提交者
  3. TSC的审查和批准

一旦一个项目被批准,他们可以立即参与在工作组的活动

## 工作组

 每个项目必须是一个单一工作组的成员。建立工作小组和位置的项目应该仔细考虑减少的风险碎片。
 该创建新的工作小组只有当它是逻辑清楚,一个项目不符合逻辑范围内现有的工作小组。

### Working Group States

| Working Groups | Description
|----------------|-----------------------
| Proposed	     | A Project has been accepted into the Mature 
|                | state, does not fit within the scope of any 
|                | other Working Group, and requires a new 
|                | Working Group to be created.
| Active         | Working Group contains one or more Projects 
|                | in the Mature state, and a Working Group Chair 
|                | is actively participating in the Technical
|                | Steering Committee.
| Archived       | Working Group has no remaining Projects in 
|                | the Mature state, and has been pruned from the
|                | project.

### Working Group State Transitions

The valid transitions (and their associated reviews) are:

| From State   | To State  | Review
|--------------|-----------|--------
| &lt;null&gt; | Active    | Creation Review
| Active       | Archived  | Termination Review

### Review Processes

For each review, there will be a publicly visible wiki/web 
template filled out containing the relevant review information, 
as outlined in the sections below.

The review document must be posted and announced for public 
comment for at least 2 weeks prior to the date the review 
is scheduled, to give participants sufficient time to decide.

Reviews ideally should be conducted in a manner that is sensitive 
to the global nature of the community (i.e., the geography and 
time zone dispersion).

#### Creation Review process and criteria

  1. Project makes request for a new Working Group during 
     [Graduation Review][graduation-review]
     a. Justifies need for a new Working Group instead of 
        joining an existing one
     b. Nominates the Working Group Chair
  2. Review by TSC and approval

[graduation-review]: #graduation-review-process-and-criteria

### Termination

Working Group termination is initiated automatically and 
immediately when the last Mature Project has been removed.
Situations in which this may occur include:

  * No Projects remain in the Mature state
  * Projects have been consolidated and transferred 
    to another Working Group

Working Groups with at least one Mature Project cannot be terminated. 

## Bootstrap Process

At the time of the formation of the Alliance, there are expected
to be several initial Projects contributed. These initial 
Projects will come in at various levels of maturity. In order 
to sort these Projects into the most appropriate Lifecycle state 
and Working Groups in a way that is clear, consistent, and fair:

  1. Prior to a date to be decided by the TSC in its first 
     meeting not less than 1 month and not more than 6 months 
     after the Alliance launches, a Project may decide to either
     a. Seek entry in ‘bootstrap’ state (explained more below)
     b. Seek entry via the steady state Project Lifecycle (ie,
        propose for Incubation)
  2. If a Project enters is in ‘bootstrap’ state, at the first
     meeting of the official (post-formation) TSC after it 
     petitions to exit bootstrap state, the TSC will decide 
     the proper Project Lifecycle state and Working Group for 
     the Project based on criteria rooted in the steady state 
     Project Lifecycle.
  3. Once in a non-bootstrap state, a Project follows the 
    steady state Project Lifecycle.

[proposal-template]: https://wiki.allseenalliance.org/develop/proposing_a_project
