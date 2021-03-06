# 关于接口规范 - 14.02

__警告:这不是最新的接口定义。__

[去这里的最新的关于接口定义] [关于-最新]
## 简介

### 目的

本文档描述的规AllJoyn&trade; 
关于界面。此接口所需的应用程序提供服务的发现机制框架接口,它支持,以及提供基本的识别信息。

### 范围

本文是针对AllJoyn应用程序开发人员。

### 引用

除了RFCs,以下是参考文档
AllSeen联盟网站上的文档/下载部分

* *AllJoyn&trade; 框架教程*
* *介绍AllJoyn; 瘦客户端*
*[RFC5646（标签辨识语言）](http://tools.ietf.org/html/rfc5646)

## 规格概述

关于接口是由应用程序实现目标设备。这个接口允许应用程序做广告,所以其他应用程序可以发现它。图1演示了一个客户端应用程序和服务应用程序之间的关系。

![关于arch][关于arch)
图:功能架构AllJoyn框架内

## 发现

客户端可以发现通过宣布这是一个应用程序无会话信号包含基本应用信息,如应用名称、设备名称、制造商、型号。公告还包含对象路径的列表和服务框架接口允许客户端是否感兴趣的应用程序提供功能。公告中还包含对象路径和服务框架接口的列表，以允许客户端来确定该应用是否提供了感兴趣的功能。

除了无会话公告，关于界面还提供了按需方法调用来检索所有关于未公布在公告中应用程序可用的元数据。

## 发现调用流

### 典型的发现流

图2展示了一个典型的调用流为客户发现一个服务应用程序,客户端只是依赖于无会话宣布决定是否连接到提供服务应用使用其服务框架。

![大致-典型-发现][about-typical-discovery]

图:典型的发现流(客户端发现服务应用程序)

### 历史进程发现流

图3演示了调用流为客户发现一个服务应用程序和要求更详细的信息。

![大致-非典型-发现][about-nontypical-discovery]

图：非典型的发现呼叫流程
##错误处理

About接口的方法调用将使用AllJoyn错误消息处理功能(ER_BUS_REPLY_IS_ERROR_MESSAGE)
设置错误的名字和错误消息。
表1列出了可能的错误提出的About界面。
**表1:About接口错误处理* *

| 错误名称 |错误消息 |
|---|---|
| org.alljoyn错误.语言不支持 | 不支持指定的语言 |

## About 界面

| 界面名称 |版本 | 担保 | 
|---|---|---|
| org.alljoyn.About | 1 | no |

### 属性

|属性名称 | 签名 | 值列表 | 可写 | 描述 |
|---|---|---|---|---|
|版本 | q | 正整数 | no | 接口版本号 |

###方法

以下方法被BusObject揭露，实现org.alljoyn 。About界面。
#### 获取About数据

**输入**

| 参数名| 强制性的 | 签名 | 值列表 | 描述 |
|---|---|---|---|---|
| 语言标记 | yes | s | 由RFC5646规定IETF语言标签。 |所需的语言。 |

**输出**

|返回签名 | 强制性的 | 描述 |
|---|---|---|
| a{sv} | yes | 可用的元数据字段的字典。不指定语言标记（即“”），将返回基于默认语言的元数据字段。 |
|  |  | 如果不支持语言标记, 错的 org.alljoyn.Error.LanguageNotSupported 被返回 |

**描述**

检索可用元数据字段的列表基于标签的语言。

#### 字段信息

表2列出了元数据字段的名称。
在公布栏将出现一个值的字段
同时也将通过宣布信号予以公布。参见[信号] [signals]
了解更多信息。
**表2:About接口数据字段**

| 字段名| 要求 |宣布 | 本地化 |签名| 描述 |
|---|---|---|---|---|---|
| 应用编号 | yes | yes | no | ay | 应用程序的全局唯一标识符。 |
| 默认语言 | yes | yes | no | s | 设备支持的默认语言。指定为RFC5646中列出的IETF语言标记。 |
| 设备名称 | yes | yes | yes | s | 特定于平台的方式（如Linux和Android）设置设备的名称。 |
|设备ID | yes | yes | no | s | 设备标识符设定的特定于平台的意思。 |
| 应用名称 | yes | yes | yes | s | 应用程序名称由应用程序制造商指定(开发人员或OEM)。|
| 制造商 | yes | yes | yes | s | 制造商的应用程序的名称。|
| 型号 | yes | yes | no | s | 该应用程序的型号。 |
| 支持的语言 | yes | no | no | as | 支持的语言列表。 |
|描述 | yes | no | yes | s | 详细描述的语言标记表示为RFC5646。 |
|生产日期 | no | no | no | s | 生产日期使用格式YYYY-MM-DD(称为XML DateTime格式)。 |
| 软件版本 | yes | no | no | s | 应用程序的软件版本 |
| AJ软件版本 | yes | no | no | s | 当前AllJoyn SDK应用程序使用的版本。 |
| 硬件版本 | no | no | no | s | 硬件版本的设备的应用程序正在运行。|
| 在线支持网址 | no | no | no | s | 支持URL（由制造商填充）。 |

#### 获取对象描述

**输入**

无。

**输出**

|返回签名 | 描述|
|---|---|
| a(oas) |返回对象路径的阵列和由每个对象提供支持的接口的列表。 |

**描述**

检索该对象路径和所实现的所有接口的列表中的每一个对象。

### 信号

| 信号名称| 参数 |无会话 | 描述|
|---|---|---|---|
| | **名称** / **签名** | | |
| 宣布 | 列表如下) | yes | 这个信号被用于宣布应用信息和它所支持的服务框架接口。以下信息中的信号提供： |
|  | 版本/ q |  | * 版本 - About界面的版本号. |
|  | 端口 / q |  | * 端口 -会话端口。这个应用程序将为传入会话监听这个端口。 |
|  | 对象描述/ a(oas) |  | * 对象描述 --数组的对象路径和每个对象提供的支持的接口列表。 |
|  | 元数据 / a{sv} |  | *元数据-元数据。列出的所有字段[字段信息][field-information]  |在宣布列是值由这个信号提供。 

## AllJoyn 内省XML

```xml
<节点名="/About" xmlns:xsi="http://www.w3.org/2001/XMLS架构-实例"
      xsi:没有名称空间模式位置="http://www.allseenalliance.org/schemas/ introspect.xsd">
   <接口名="org.alljoyn.About">
      <属性名="版本" type="q" access="read"/>
      <方法名="获取 About数据">
         <参数名称="语言标记" type="s" direction="in"/>
         <参数名称="About数据" type="a{sv}" direction="out"/>
      </方法>
      <方法名="获取对象描述">
         <参数名称="对象描述" type="a(sas)" direction="out"/>
      </方法>
      <信号名称="宣布">
         <参数名称="版本" type="q"/>
         <参数名称="端口" type="q"/>
         <参数名称="对象描述" type="a(sas)"/>
         <参数名称="元数据" type="a{sv}"/>
      </信号>
   </接口>
</节点>
```

[about-latest]: /learn/core/about-announcement/interface


[about-arch]: /files/learn/about-arch.png
[about-typical-discovery]: /files/learn/about-typical-discovery.png
[about-nontypical-discovery]: /files/learn/about-nontypical-discovery.png

[field-information]: #field-information
[signals]: #signals
