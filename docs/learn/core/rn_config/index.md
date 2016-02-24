# 路由节点配置文件

路由节点(RN)配置文件是一个XML文件控制RN的行为和设定某些变量,如定时器、连接
限制,和用例特征。

示例配置文件可以在AllJoyn源代码分发INC找到
`../alljoyn_core/router/test/conf/sample.conf`

请注意，此示例配置文件将不会在实践中有用。它仅仅是旨在提供一个例子：各种元素和属性是如何被格式化的。

## XML模式

XML模式,可以用来验证配置文件,可能在AllSeen联盟网站上找到:

https://allseenalliance.org/schemas/busconfig.xsd

该模式也是在源代码分发可供选择：
`../alljoyn_core/docs/busconfig.xsd`

```xml该架构可以通过包括以下属性在`busconfig` (例如, root)因素引用配置文件：

<busconfig
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:noNamespaceSchemaLocation="https://allseenalliance.org/schemas/busconfig.xsd">
```

##默认配置

### 捆绑路由节点
捆绑路由节点由一个路由节点和一个标准的和在运行单个进程中标准的客户端应用程序组成。

15.9版本中,作为v15.09的，用户可以编译AllJoyn与scons的变量`TEST_CONFIG`设置为一个配置文件的绝对或相对路径。然而，通过scons的变量名作为建议，这种方法是用于调试和测试目的。

如果用户不提供一个配置文件,然后捆绑RN依赖于硬编码配置文件中包含“BundledRouter.cc”。
注意,所有配置设置,适用于独立的捆绑也适用于RN。

 **捆绑RN硬编码配置:**
 ```xml
<busconfig>
    <type>alljoyn_bundled</type>
    <listen>tcp:iface=*,port=0</listen>
    <listen>udp:iface=*,port=0</listen>
    <limit name="auth_timeout">20000</limit>
    <limit name="max_incomplete_connections">4</limit>
    <limit name="max_completed_connections">16</limit>
    <limit name="max_remote_clients_tcp">8</limit>
    <limit name="max_remote_clients_udp">8</limit>
    <property name="router_power_source">Battery powered and chargeable</property>
    <property name="router_mobility">Intermediate mobility</property>
    <property name="router_availability">3-6 hr</property>
    <property name="router_node_connection">Wireless</property>
</busconfig>
```
### 独立的路由节点
独立运行RN命名为Linux上的`alljoyn-daemon`编译的应用程序。如果在alljoyn守护程序启动时用户没有指定一个单独的配置文件，或使用`--internal`开关，那么RN将使用在源硬编码的配置。

注意：独立RN也可作为在Windows10的服务。Windows10的服务使用硬编码的配置，并且不接受任何参数或开关。描述传递参数或选项为`alljoyn-daemon`本文档中的例子指的是Linux实现。

**例子 (Linux):** 开始使用内部配置路由节点

    ./alljoyn-daemon

        --OR--

    ./alljoyn-daemon --internal

**独立的RN硬编码配置 (Linux):**
```xml
<busconfig>
    <type>alljoyn</type>
    <listen>unix:abstract=alljoyn</listen>
    <listen>tcp:iface=*,port=9955</listen>
    <listen>udp:iface=*,port=9955</listen>
    <limit name="auth_timeout">20000</limit>
    <limit name="max_incomplete_connections">16</limit>
    <limit name="max_completed_connections">32</limit>
    <limit name="max_remote_clients_tcp">0</limit>
    <limit name="max_remote_clients_udp">0</limit>
</busconfig>

```

## 使用外部配置文件
如前面提到的，用户定义的，外部配置文件可以被传递到一个独立的RN。为了与外部配置文件启动独立RN，输入以下命令（Linux）：

```
./alljoyn-daemon --config-file=./my_rn_config.xml
```

## 最低配置文件

一个最小的配置文件需要根元素（`<busconfig>`）和单`<listen>`元素。

例如:
```xml
<busconfig>
    <listen>tcp:iface=*,port=9955</listen>
</busconfig>

```

这将导致有效的、但有非常基本功能的配置文件。实际上，这种配置文件会防止连接任何叶节点，因为它不会覆盖0的默认值.
`max_remote_clients_tcp`和`max_remote_clients_udp`（即`<限制>`属性）。
因此，只能够连接到其它路由节点。...

## 配置文件的元素和属性的回顾

 默认配置设置应该适合大多数用例。然而，一些应用可能受益于使用不同的设置。因此，下面的信息是为了在使用这些设置提供一定指引。

以下是顶级XML元素的字母顺序列表（即根元素'的直接子元素`<busconfig>``）可用于在配置文件中使用，具有自己的意义和应用的描述。任何子元素将它们的父元素下面描述。同样的属性。

### ``<auth>``
不是由AllJoyn使用。包括为了向后兼容。包括这配置文件中的元素将没有影响。

### ``<flag>``

**注意:**

标记元素的内容可以是“真”或“假”。所有的旗帜
默认为“假”

#### 名称属性(必需的)

|名字              |描述|默认的|注释|
|------------------|-----------|-----------------------|--------|
|ns_enable_v1      |如果真|真|用于调试使用传统名称服务。不要使用在部署生产应用程序。|
|ns_disable_ipv4   |如果真|假|用于调试，禁用IPv4多播。不要使用在部署生产应用程序。|
|ns_disable_ipv6   |如果真|假|用于调试，禁用IPv6多播。不要使用在部署生产应用程序。|
|ns_disable_directed_broadcast   |如果真|假|用于调试，禁用子网广播。不要使用在部署生产应用程序。|

#### E例子
1. ``<flag name="ns_enable_v1">false</flag>``
1. ``<flag name="ns_disable_ipv6">true</flag>``

### ``<fork>``

叉元素是空的。它不包含任何内容、子元素或属性。如果配置文件中,路由节点(守护进程)将运行在
背景。

如果出现在配置文件中，路由节点（守护进程）将在背景中运行。

需要注意的是`<叉>`必须按顺序使`<pidfile>`是有效的。

在Linux上，可从命令行中覆盖配置文件设置`<叉>`，通过使用`--fork`或`--no-fork`选项。

#### 例子
1. ``<fork/>``

### ``<include>``

指定路径到另一个配置文件,其内容将包括在“<包括>的“插入的元素中。

指定的路径可能是绝对的或相对的。如果路径是相对的
它是相对于配置文件使用“<包括> ' '。

此外,路径必须是文件扩展名的文件' ' . conf ' '。

#### 忽略_缺少的属性（可选）
可以设置为“是”或“否”。默认为“否”。

|忽略_缺少的|描述|
|--------------|-----------|
|no (default)  |如果该文件不存在，会发生致命错误|
|yes           |如果该文件不存在,默默地忽略|

#### 例子

1. ``<include>../configs/my_config.conf</include>``
1. ``<include ignore_missing="yes">my_config.conf</include>``
1. ``<include ignore_missing="no">/home/bob/configs/my_config.conf</include>``

### ``<includedir>``

类似于“<包括> ' ',除此之外,而不是给一个文件指定路径，指定一个路径给目录包含配置文件
与``.conf``文件扩展名的所有文件都将被包括在内。

请注意，在该目录中的文件包含的顺序是不确定的，因此，使用该元素时，用户应避免任何配置文件包括顺序依赖性。

指定的路径可以是绝对或相对的。如果路径是相对的，那么它是相对于配置文件使用
``<includedir>``

#### 忽略_缺少的属性（可选）
可以设置为“是”或“否”。默认为“否”。

|忽略_缺少|描述|
|--------------|-----------|
|no (default)  |如果该目录不存在，会发生致命错误|
|yes           |如果目录不存在，默默地忽略|

#### 例子
1. ``<includedir>../configs</includedir>``
1. ``<includedir ignore_missing="yes">configs</includedir>``
1. ``<includedir ignore_missing="no">/home/bob/configs</includedir>``

### ``<limit>``
设置不同的超时和连接限制变量为无符号，32位整数。

#### 名称属性(必需)
|名称|描述|默认的|
|----|-----------|
|auth_timeout|允许的最长时间传入的连接来完成身份验证。超出这个极限,传入的连接可能会中止。|20000 (ms)|
|session_setup_timeout|允许的最长时间传入的连接设置会话路线。超出这个极限,传入的连接可能会断开。|30000 (ms)|
|max_incomplete_connections|传入的连接的最大数量,可以在验证的过程中。如果开始一个新的连接进行身份验证，将意味着超过这个数,那么新的连接将下降。|10 (connections)|
|max_completed_connections|允许每个传输连接（入站和出站）的最大数量。这意味着允许TCP和UDP完成的连接的总数目是该值的两倍。<br><br>如果在开始处理一个新的连接将意味着超过该号码，然后新连接将被丢弃。|50 (connections) |
|max_remote_clients_tcp|远程客户端使用TCP的最大数量。< br > < br >注意:在使用外部配置文件来启用TCP连接叶节点时，这个值_必须_被覆盖。 默认的内部配置文件已经覆盖这个值。请注意此设置可能对应用来说比较有用且有特定的要求，以防止叶节点无法连接应用；<br><br>然而，对于一般的RNs，这个值不应该为零。|0 (connections)|
|max_remote_clients_udp|使用UDP的远程客户机的最大数量。<br><br>注意:这个值_必须_被覆盖使用外部配置文件,以使UDP连接叶节点。默认的内部配置文件已覆盖此值。<br><br>注意该设置可能对应用非常有用且有特定的要求，以防止叶节点无法连接。然而，对于一般的 RNs，这个值不应该为零。|0 (connections)|

##### 测试钩 _唯一的_
在源代码中发现的许多限制是钩子，对于测试 AllJoyn 以及应用程序开发者或最终产品是不适用的。 
为了信息的目的,下面列出了这些,但是,请不要在生产代码中使用它们作为他们可能有不良的或不可预知的行为。

|**不要使用**| |
|----------------------------------|-|
|slap_min_idle_timeout|udp_timewait|
|slap_max_idle_timeout|udp_segbmax|
|slap_default_idle_timeout|udp_segmax|
|slap_max_probe_timeout|udp_fast_retransmit_ack_counter|
|slap_default_probe_timeout|sls_backoff|
|udp_connect_timeout|sls_backoff_linear|
|udp_connect_retries|sls_backoff_exponential|
|udp_initial_data_timeout|sls_backoff_max|
|udp_total_data_retry_timeout|sls_preferred_transports|
|udp_min_data_retries|tcp_default_probe_timeout|
|udp_persist_interval|tcp_min_idle_timeout|
|udp_total_app_timeout|tcp_max_idle_timeout|
|udp_link_timeout|tcp_default_idle_timeout|
|udp_keepalive_retries|tcp_max_probe_timeout|
|udp_delayed_ack_timeout|dt_min_idle_timeout|
|dt_max_idle_timeout|dt_default_idle_timeout|
|dt_max_probe_timeout|dt_default_probe_timeout|

#### 例子
1. ``<limit name="auth_timeout">20000</limit>``
1. ``<limit name="max_incomplete_connections">16</limit>``

### ``<listen>``
标识一个地址总线的附件应该侦听传入的连接。这个地址必须是一个有效的URI,始于一个AllJoyn运输名称,其次是合适的参数和选项。

配置文件必须包含至少一个`<listen>`元素。  此外,一个配置文件可能包含多个`<listen>`的元素, 包括同一个传送，只要指定的传输和其他参数是有效的，由主机平台支持。

重复 `<listen>` 元素将被忽略(一条警告消息)。无效的 `<listen>` 元素将导致一个致命错误, 除非有一个有效的 `<listen>` 的元素,在这种情况下,无效的 `<listen>` 素将被忽略(一条警告消息)。

#### 描述和格式

|协议或传输的名字|描述|格式|例子|
|--------------------------------------------------------|
|tcp|AllJoyn over TCP|`tcp:(iface=<interface>` &#124; `addr=<IPv4 address),port=<port number>`|`tcp:iface=lo,port=9955`|
|udp|AllJoyn over UDP|`udp:(iface=<interface>` &#124; `addr=<IPv4 address),port=<port number>`|`udp:iface=*,port=9955`|
|unix|Unix域套接字。只适用于POSIX平台的独立路由节点。|`unix:(abstract` &#124 `path)=<named socket>`|`unix:abstract=alljoyn`|
|slap|AllJoyn协议的串行线。用于在一个在AllJoyn嵌入式平台和一个基于posix的路由节点之间提供一个接口。|`slap:type=<com interface type>,dev=<serial port>,baud=<baud rate>`|`slap:type=uart,dev=/dev/ttyUSB0,baud=115200`|
|npipe|Windows 10命名管道协议|`npipe:`<br>(注意:没有可用的选项或其他参数)|`npipe:`|

#### 格式选项

|选项名称|内容|使用通配符?|注意|
|--------------------------------|
|iface| 有效的网络接口名（参见RFC2863 ifName`的`定义）承载RN设备。例如，`eth0`在Linux或Windows`以太网32803`。 |Yes|“iface”和“addr”是互斥< br >“iface”优先于“addr”|
|addr|有效的 IPv4 地址|Yes|“iface”和“addr”是互斥< br >“addr”主要存在向后兼容性。更喜欢“iface”。|
|port|有效的TCP或UDP端口号|No|`port=0` 表明,传输将使用一个临时端口。|
|abstract|指定套接字|No|仅Linux结果`UNIX：抽象= alljoyn` 是默认的，因为应用程序在默认情况下查找"alljoyn" 套接字名称|
|path|POSIX命名的套接字|No|指定的路径必须参考现有的套接字。|
|type|通讯接口类型。|No|此时，只有`uart`被支持。|
|dev|串行端口|No|特定的POSIX平台。<br>只在Linux上进行测试。|
|baud|指定的串口波特率。|No| |

### ``<pidfile>``
记录路由节点进程ID（PID）到指定的文件。如果该文件不存在，它将被创建。如果该文件确实存在，它会被覆盖。

**重要提示：** 如果`<叉>`还指定，`<pidfile进程文件>`是唯一有效的。可替换地，路由节点可以与`--fork`选项（Linux）一起启动。

### ``<policy>``
定义一个安全策略到总线应用到一组特定的连接。 政策由 ``<allow>`` 和 ``<deny>``
元素组成. 政策类似于一个防火墙,它们允许预计流量和防止意想不到的流量。

当政策重叠时，后来的政策应用将覆盖之前的应用。

#### 属性
 `<policy>`元素必须包含以下属性之一,且是相互排斥的。 如果不止一个属性存在，将产生致命的错误。

|属性|内容|优先级|
|---------------------|---------------|
|context  |“默认”或“强制性”|`背景=“默认”`是最低优先级。如果没有一个规则和其他类别相匹配，这类策略规则适用。<br><br>`背景=“强制性”`是最高优先级。 此类别中的策略规则凌驾于所有其他类别的规则。|
|user     |用户名或用户id|`user="uid"` 比背景优先级高=“默认”和组="gid", 比背景优先级地低="强制". 此类别中的策略规则覆盖背景=“默认”和组="gid".|
|group    |组名称或gid|`组="gid"` 比背景优先级高="默认" 比优先级用户低"uid". 这类策略规则覆盖背景=“默认”。|


#### `<allow>` and `<deny>`
A `<policy>` 必须至少包括一个 `<allow>` 或 `<deny>` 元素.  然而，
一个`<policy>` 只有一个 `<allow>` 元素对AllJoyn是毫无意义的,因为所有的传输在默认情况下是允许的。 相反,对于之前的 `<deny>` 元素，一个 `<allow>` 元素主要功能是个例外.

#####  `<allow>` 和 `<deny>` 元素的属性

|属性        |内容|例子|注释|
|--------------------------|
|user|用户名或用户标识|`user="joes"`|在POSIX设备中,允许或不允许应用程序在相同的设备连接到总线。<br><br>可以使用通配符, 例如 `user="*"`|
|group|组名或 gid|`group="enemies"`|在POSIX设备中,允许或不允许应用程序在相同的设备连接到总线。<br><br>可以使用通配符, e.g. `group="*"`|
|own|总线名称|`own="com.companyA.ProductA"`|允许或不允许叶节点请求总线名称的所有权。<br><br>使用反向域名表示法。可以使用通配符, 例如 `own="*"`|
|own_prefix|总线名称前缀|`own_prefix="com.companyA"` 将匹配 `com.companyA.productA` 和 `com.companyA.productB`|允许或不允许叶节点请求总线名称的所有权。<br><br>使用反向域名表示法。|
|send_error / 接收_错误|启用或禁用特定错误消息发送或接收|`receive_error="org.alljoyn.Error.Foo"`|使用反向域名表示法。可以使用独立的通配符, 例如 `send_error="*"`|
|send_interface / receive_interface|启用或禁用特定的接口来发送或接收|`receive_interface="com.companyA.InterfaceB"`|使用反向域名表示法。 可以使用独立的通配符, 例如 `send_interface="*"`|
|send_member / receive_member|启用或禁用发送或接收信号或方法|`receive_member="some_signal_name"`| |
|send_path / receive_path|启用或禁用特定对象路径发送或接收|`send_path="/org/alljoyn/lighting"`| |
|send_path_prefix / receive_path_prefix|启用或禁用所有对象路径匹配一个特定的前缀|例如 `send_path_prefix="/org"` 将匹配 `/org/alljoyn`, `/org/CompanyA`, etc.||
|send_group / receive_group|组名称或GID。 如果发送或接收组匹配指定的组，规则将匹配。|`send_group="mycompany"` | |
|send_user|用户名或uderid。 如果接收端点匹配指定的用户，规则将匹配。|`send_user="joes"`| |
|receive_user|用户名或uderid。如果发送端点匹配指定的用户，规则将匹配。|`receive_user="beth"`| |
|send_same_user / receive_same_user|指定是否相同的用户凭据与每个连接应用程序相关联,或不关联。必须“true”或“false”。|例如 `send_same_user="false"`||
|receive_sender / send_destination|启用或禁用发送或接收,特定的总线名称|`receive_sender="com.companyA.productB"`|将匹配任何相关的别名,或独特的名字|
|send_type / receive_type|启用或禁用消息类型。必须是 "method_call", "method_return", "signal", 或 "error"|`send_type="method_call"`| | |

#### 例子
```xml
<policy context="default">
  <deny user="*"/>
  <deny own="*"/>

  <deny send_type="method_call"/>
  <allow send_type="signal"/>
  <allow send_type="method_return"/>
  <allow send_type="error"/>

  <allow send_destination="org.freedesktop.DBus"/>
  <allow receive_sender="org.freedesktop.DBus"/>

  <allow send_interface="org.alljoyn.Bus.Peer.Session"/>

  <allow user="jethro"/>

</policy>

<policy user="jethro">
  <allow send_type="method_call"/>
  <allow send_type="signal"/>
  <allow send_type="method_return"/>
  <allow send_type="error"/>

  <allow own_prefix="test"/>
</policy>

<policy user="joe">
   <deny send_type="method_call" send_user="beth"/>
   <deny receive_type="signal" receive_user="bob"/>
</policy>
```

### ``<property>``
定义特征相关的路由器节点选择和名称服务。

#### name attribute (required)
|名称|描述|默认|注释|
|----|-----------|:-----:|-----|
|router_node_connection|用于路由节点选择。<br><br>下列之一:<br>"access point", "wired", "wireless"|"wireless"|如果其中一个没有使用枚举值,值将被忽略,一条警告消息将被记录,将使用默认值|
|router_availability|用于路由节点选择。<br><br>下列之一:<br>"0-3 hr", "3-6 hr", "6-9 hr", "9-12 hr", "12-15 hr", "15-18 hr", "18-21 hr", "21-24 hr" |"3-6 hr"|如果其中一个没有使用枚举值,值将被忽略,一条警告消息将被记录,将使用默认值|
|router_mobility|用于路由节点选择.<br><br>下列之一:<br>"总是固定的", "低流动性", "中间移动", "高迁移率"|"中间移动"|如果其中一个没有使用枚举值,值将被忽略,一条警告消息将被记录,将使用默认值|
|router_power_source|用于路由节点选择。<br><br>下列之一:<br>"总交流供电", "电池供电和收费", "电池供电,不收费"|"电池供电和收费"|如果其中一个没有使用枚举值,值将被忽略,一条警告消息将被记录,将使用默认值|
|router_advertisement_prefix|用薄的核心应用程序(TCA)发现路由节点。|``"org.alljoyn.BusNode."``|为了最大化TCA的可用性,通用路由节点不应该修改这个值。然而,当部署自定义系统,由于特定性能的限制，需要限制TCA连接到路由节点时它可能有用|

#### 例子
```xml
<property name="router_power_source">电池供电和收费</property>
<property name="router_mobility">中间移动</property>
<property name="router_availability">3-6 hr</property>
<property name="router_node_connection">无线</property>
```

### ``<syslog>``
没有值或属性。如果存在,表明AllJoyn守护进程应该给`syslog`发送日志消息。

#### 例子
``<syslog/>``

### ``<type>``
Not used by AllJoyn.  Included for backward compatibility.  Including this
element in the config file will have no effect.
不由AllJoyn使用。包括为了向后兼容。包括配置文件中的这种元素将没有任何效果。

### ``<user>``
表示AllJoyn守护进程应指定用户，而不是root身份运行。
请注意，指示用户必须知道该系统。

#### 例子
`<user>barts</user>`
