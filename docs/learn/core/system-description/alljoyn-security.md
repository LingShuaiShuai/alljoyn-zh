# AllJoyn&trade; 安全

## 综述

该AllJoyn系统为应用程序提供一个安全框架相互认证和它们之间发送加密数据。在AllJoyn框架提供终端到终端的应用层安全。身份验证和数据加密是在应用程序完成的。这些应用程序可以驻留在同一设备上或在不同设备，并且可以连接到相同AllJoyn路由器或不同的AllJoyn路由器。

* *注意:* *没有完成身份验证AllJoyn路由器层。
AllJoyn框架支持安全级别的接口。应用程序可以标记一个接口为“安全”，以使之身份验证和加密。
所有的方法中，信号，和一个安全界面的性质被认为是安全的。当消费者应用程序调用安全接口上的方法调用，或明确地调用的API时候，身份验证 - 和加密相关的密钥交换，以确保与远程对等应用程序的连接需求启动。

下图显示了高层次的AllJoyn的安全架构。

![alljoyn-安全-拱][alljoyn-security-arch]

**图:* * AllJoyn安全架构

身份验证和加密是在应用程序层完成的。
AllJoyn核心库实现的所有逻辑进行身份验证和加密除了Auth侦听器。Auth侦听器是一个回调函数实现的应用程序提供身份验证凭据(如。PIN或密码)或验证身份验证凭据(如，在ALLJOYN_ECDHE_ECDSA中验证证书链。）身份验证和加密密钥存储在密钥存储管理的安全模块中。

* *注意:* * AllJoyn路由器只是参与传输
与安全相关的应用程序端点之间的消息。它本身没有实现任何安全逻辑。

简单身份验证和AllJoyn框架使用安全层(SASL)安全框架进行身份验证。它使用d - bus的身份验证协议(d-bus规范)(http://dbus.freedesktop.org/doc/dbus specification.html)交换身份验证相关数据。

AllJoyn框架支持以下身份验证机制应用到应用级别的身份验证:

* ALLJOYN_SRP_KEYX - 安全远程密码(SRP)密钥交换
* ALLJOYN_SRP_LOGON -安全远程密码(SRP)与用户名和密码登录
* ALLJOYN_ECDHE_NULL - 椭圆曲线diffie - hellman(短暂)没有认证密钥交换
* ALLJOYN_ECDHE_PSK -  椭圆曲线diffie - hellman(短暂)密钥交换身份验证pre-shared键(相移键控)
* ALLJOYN_ECDHE_ECDSA -椭圆曲线diffie - hellman密钥交换(短暂)验证一个X.509 ECDSA证书


AllJoyn框架还支持匿名和外部身份验证机制作为d - bus规范定义

*两个AllJoyn路由器之间使用匿名身份验证机制为空验证。它也用于身份验证一个薄应用和一个AllJoyn路由器之间。
**使用外部身份验证机制之间的应用及安装AllJoyn路由器(独立AllJoyn路由器)在Linux平台上。

### 安全在14.06版本中的变化

14.06版本的发布,ALLJOYN_PIN_KEYX身份验证机制从AllJoyn薄核心库中删除。
这种身份验证机制仍然被AllJoyn标准核心库支持。

以下新的椭圆曲线的Diffie-Hellman短暂（ECDH）的身份验证机制进行补充道：

* ECDHE_NULL没有身份验证是关键协议
* ECDHE_PSK是一个关键的协议验证pre-shared对称密钥。
* ECDHE_ECDSA是一个关键的协议认证与非对称密钥验证ECDSA签名。

在瘦薄应用程序和标准应用程序中都可以使用这些新的身份验证机制。薄应用程序的14.06版本只支持ECDHE-based身份验证机制。

在14.06版本中，使用SASL协议认证从AllJoyn瘦核心库中除移,并将继续支持AllJoyn标准核心库。

关于这些变化的更多信息,请参见最新版本的[安全HLD](https://wiki.allseenalliance.org/core/security_enhancements).

### 安全15.04版本的变化


在15.04版本中,ALLJOYN_PIN_KEYX和ALLJOYN_RSA_KEYX从标准客户端身份验证机制已被移除。对ECDSA x
的支持被补充了。

# #安全概念

本节定义了AllJoyn安全相关概念。

# # #认证(Auth)全局唯一标识符

身份验证全局唯一标识符是分配给应用程序的全局唯一标识符，来进行身份验证的这个全局唯一标识符坚持密钥存储,为应用程序提供了一个长期的身份。通常,这全局唯一标识符与单个应用程序相关联。在方案中，其中一组相关的应用程序共享一个给定的密钥存储，他们也有着相同的全局唯一标识认证。

该全局唯一标识用作映射键来存储和访问身份验证和加密密钥相关联的应用程序。

###主密钥

主秘钥是一个经过身份验证的对等应用程序之间共享的关键。
两个对等的应用程序独立生成相同的主秘钥,并持续存储它在密钥存储库中。


主秘钥被存储在每个认证全局唯一标识符的对等应用程序中,和应用程序都有一个关联、可变的TTL。一旦主秘钥是有效的,对等应用程序不必一对一重新认证来互相交换加密数据。

主密钥长度为48字节按照[RFC5246](http://www.rfc-base.org/txt/rfc-5246.txt).

### 会话密钥

一个密钥用于加密点对点两个对等的应用程序之间的数据流量。单独的会话密钥维护每个连接对等应用程序。一个会话密钥是有效的,只要同行(超过任何AllJoyn会话)相连。它是在密码意义上的会话，并没有涉及到一个AllJoyn会话。相同的会话密钥是在所有AllJoyn会话使用的两个邻居。

会话密钥存储在存储器中，它们不存在。
生成会话密钥的对等的应用已经经过
验证，并且当连接终止与对等到期要对等应用程序。会话密钥是从主密钥导出，用于加密方法调用，方法回复，和单播信号。

会话密钥是128位长。

* *注意:* *当前的实现有一个默认的TTL两天的会话密钥。如果应用程序保持联系很长时间,相关的会话密钥和一个新的会话密钥到期需要生成。

### 组密钥

组密钥是一个用于加密由提供商应用程序发送出点对多点的数据流量（广播信号）的加密密钥。一组的单一的组密钥是由应用程序加密广播信号发送到每个对等连接应用程序。

当一个应用程序生成，生成一组关键对于任何连接同行的第一个会话密钥。组密钥生成总是独立于应用程序的提供者或使用者的角色。只有提供者应用程序使用该密钥发送加密的广播信号。应用程序交换组键使用一个涉及到会话密钥加密的方法调用。

该组密钥是128位长,随机生成的。该组密钥在本质上是定向。每个应用程序都有自己的组密钥来加密广播信号。此外,它还维护一个单独的同龄群体主要为每个连接对等的解密广播信号收到他们。此外，它也保持为每个所连接的对等体，用于解密从它们接收的广播信号的独立对等体组密钥。

组密钥存储在内存中,它们不被保留。该组与对等应用程序连接结束时到期。应用程序的组密钥到期时，不再有任何同行联系。当应用程序不再和同行有联系时，远程对等组密钥将到期。

**注：**在未来的版本中，组密钥或继续支持无会话信号的加密。

### 密钥存储

密钥存储是用于持久存储本地存储认证相关密钥，以及存储主密钥和相关TTL。应用程序可以提供自己的实现密钥存储或使用由AllJoyn系统提供的默认密钥库。

设备上的多个应用程序可以共享一个给定的密钥存储。在这种情况下，这些应用程序共享同一组的验证密钥。在当前实现中,用密钥加密的密钥存储库中的内容源自密钥存储路径。

 对于每一个应用程序认证，密钥存储保持主密钥和每个身份验证全局唯一标识应用程序的相关TTL。也是维护分配给正在使用的密钥存储在本地应用程序的权威性GUID。

下面的表显示了一个示例与主秘密密钥存储库中存储两个对等的应用程序。

#### 密钥存储库与主秘密存储两个对等的应用程序示例

**本地 身份验证 全局唯一标识符 - 全局唯一标识符x**

| 对等认证 全局唯一标识符| 主密钥 | TTL |
|---|---|---|
| 全局唯一标识符1 | MS1 | T1 |
| 全局唯一标识符2 | MS2 | T2 |

## 端到端安全流

下图捕获为AllJoyn安全的使用情况的高层次的端至端的消息流，当两个应用程序都不能互相验证之前。安全消息流发起基于以下触发器之一：

*消费者应用程序调用一个安全的方法给远程服务对象或
*消费者应用程序明显地调用一个API来保障与远程对等的连接。

![e2e-security-flow-two-unauth-apps][e2e-security-flow-two-unauth-apps]

**图:**端到端安全流(两个应用程序没有相互身份验证之前)

它是理想的应用程序总是明确地保护与对端的连接。在当一个应用程序仅仅是对接收安全的信号有兴趣的话，那就是保护与对端的连接，以获得钥匙解密信号的唯一途径。

连接与应用程序的AllJoyn核心库实现了所有的AllJoyn安全逻辑。AllJoyn路由器只充当一个传递安全相关的消息。每个应用程序需要调用启用对等安全性的API调用与AllJoyn核心库，以使AllJoyn具有安全性。应用程序使用指定的身份验证机制,身份验证回调侦听器和密钥存储文件作为API调用的一部分。它还表明是否可以共享密钥存储库。AllJoyn核心库生成应用程序的身份验证全局唯一标识符，作为首次密钥存储库初始化的一部分。身份验证全局唯一标识符被存储在密钥存储库中。

与提供者应用程序建立一个会话后,消费者应用程序启动上述安全触发器之一。AllJoyn核心库检查是否认证已经在进行中。

*如果是的,它停了下来。
*如果不,继续安全流动。

它看起来为远程对等的关键材料。在这种情况下,因为这是第一个安全交互与远程应用程序,没有找到关键材料。这将触发安全流与远程对等。

消息流包含以下四个不同的步骤:

1. 交换身份验证 全局唯一标识符: 这一步涉及到对等应用程序之间交换身份验证全局唯一标识符。一旦学会了,远程应用身份验证全局唯一标识符将被用来监督主秘钥是否存在于密钥存储库中的身份验证全局唯一标识符。在本例中,没有找到主秘钥,因为两个应用程序没有相互验证。

2. 应用到应用的认证:这一步涉及两个同行验证对方其中一个身份验证相互验证机制。在这一步的结尾,两个同伴相互验证,现在共享一个共同的主密钥。

3. 生成会话密钥:这一步涉及两个同行生成会话密钥用于加密点对点消息之间的安全。会话密钥生成独立的基于共享主键的同行。产生第一个会话密钥时，也会生成一组密钥。

4. 交易组密钥: 这一步涉及两个同伴相互交换自己的组密钥通过一个加密AllJoyn消息。所述AllJoyn消息是利用两个对等端之间已经建立的会话密钥进行加密。应用程序使用组密钥加密会话多播和广播信号。在此步骤结束时，两个对等应用程序具有组密钥来解密从彼此接收到的安全广播信号。

对每个步骤的细节在下面的章节中都进行了描述。完成这些步骤之后,对等应用程序现在已经建立了加密/解密密钥交换加密的方法调用和信号。

这些密钥作为对等状态表,，其中包括远程对等的唯一名称，以及当前应用程序的本地身份验证全局唯一标示符和组密钥的一部分来管理。

下表提供存储了两个认证对等应用程序密钥的示例对等体状态表。

#### 两个互相验证的对等的应用程序的样本对等状态表

**本地认证全局唯一标示符 - 全局唯一标示符**
**应用程序组密钥 - GKx**

| 对等认证全局唯一标示符 |唯一名称 |会话密钥 | 对等组密钥 |
|---|---|---|---|
| GUID1 | :100.2 | SK1 | GK1 |
| GUID2 | :200.2 | SK2 | GK2 |

## 已通过身份验证的应用程序

当应用程序连接彼此之后他们第一次连接时,如果主秘密仍然是有效的，他们不需要再次验证彼此。下图显示了这个用例的安全流动。

![认证 - 应用程序 - 重新连接][authenticated-apps-reconnecting]

**图:**再次验证应用程序连接

在这种情况下,由于发现远程对等的应用程序没有连接,没有关键材料。 这样一来, 消费者应用程序执行与远程对等交换身份验证全局唯一标示符这一步.
这个检索远程对等的身份验证全局唯一标示符可用于查找密钥存储文件。

由于应用程序已经通过身份验证，一个主秘钥被发现在密钥存储库中，为了身份验证全局唯一标示符的提供者应用程序 以及没有出现应用程序间互相验证的需要。 消费者应用程序直接进入下一步生成会话密钥和/或组密钥。

**注：**如果验证步骤会话密钥的生成过程中出现故障，消费者应用程序必须与供应商重新认证，即使主密钥仍然是有效的。

## 交换身份验证全局唯一标识符

下图显示了消息流对等应用程序之间交换身份验证的全局唯一标识符。

![交换-验证-全局唯一标识符][exchange-auth-guids]

**图:** 交换身份验证的全局唯一标识符

消息流的步骤如下所述。
The message flow steps are described below.

1. 消费者应用程序生成一个交换全局唯一标识符的METHOD_CALL消息并将其通过AllJoyn路由器发送到提供者应用程序。这个消息包括消费者应用程序的身份验全局唯一标识符和最大的身份验证消费者应用程序支持的版本。

2.如果它不支持从消费者应用程序收到的权威性的版本，供应商的应用程序提出了最高权威性的版本。

3. 该供应商的应用程序生成一个交换全局唯一标识符METHOD_RETURN消息并将其通过AllJoyn路由器发送到消费者应用程序。这个消息包括身份验证提供者应用的全局唯一标识符以及身份验证供应商的应用程序最大的版本。

4.消费者应用验证,它支持获得身份验证的版本。这就完成了交换全局唯一标识符的一步。

##应用到应用的认证

AllJoyn对等应用程序彼此进行身份验证时使用的身份验证机制详细的在这一节中。这些身份验证机制的设计是基于安全构造(RFC 5246)(http://www.rfc - base.org/txt/rfc - 5246. - txt)和(RFC 5054)(http://www.rfc-editor.org/rfc/rfc5054.txt)。适用的RFC部分在描述这些身份验证机制的细节时列出。

* *注:* *为身份验证消息流可在本节中见到，使用者和提供者应用程序也被称为客户机和服务器，分别与术语用于(RFC 5246)(http://www.rfc - base.org/txt/rfc - 5246. - txt) 和 (RFC 5054)(http://www.rfc editor.org/rfc/rfc5054.txt)。

### D-SASL总线协议的使用

在AllJoyn框架实现的D-Bus SASL交换协议[D-总线规范（http://dbus.freedesktop.org/doc/dbus-specification.html）交换验证相关的数据。所有认证相关的交换通过AuthChallenge方法调用完成/回复，定义为org.alljoyn.Bus.Peer.Authentication的一部分。接口由AllJoyn核心库实现。

身份验证数据交换作为SASL字符串生成基于d - bus SASL交换协议。身份验证数据在SASL字符串发送十六进制形式。然后生成的字符串作为参数传递给AuthChallenge方法调用或方法的回复。

例如，要启动对ALLJOYN SRP KEY认证，生成的字符串是：
```
"AUTH ALLJOYN_SRP_KEYX <c_rand in hex>"
```

这包括SASL AUTH命令,身份验证机制,以十六进制形式的身份验证数据。

下表捕获D-Bus SASL命令由AllJoyn框架的支持。

#### d - bus SASL AllJoyn框架支持的命令

| 命令 | 方向 | 描述 |
|---|---|---|
| 身份验证[机制] [最初——反应] | 消费者->供货商| 开启验证 |
| 取消 | 消费者->供货商 | 取消验证。 |
| 开始 | <ul><li>消费者->供货商</li><li>供货商->消费者</li></ul> | <ul><li>在消费者方面，确认了消费者已经从提供者接收的一个OK的命令，该消息流即将开始。</li><li>|在供应商方面,由供应商发送的响应开始引起消费者的需求。
</li></ul> |
| 数据 | <ul><li>消费者->供应商</li><li>供应商->消费者</li></ul>在消费者或提供者方面，包含数据的一个十六进制编码的块来根据所使用的身份验证机制来解释。|
| OK | 消费者->供应商 | 客户端已经通过身份验证。 |
| 拒绝 | 消费者->供应商 | 在消费者方面,表明当前交换身份验证失败,并进一步的数据交换是不合适的。消费者尝试另一个机制,或尝试提供不同的应对挑战。 |
| 错误 | <ul><li>消费者->供应商</li><li>供应商->消费者</li></ul>|在使用者或提供者方面,提供者或消费者不知道命令,在当前背景下,不接受给定的命令,或不了解的命令的参数。 |

### ALLJOYN_SRP_KEYX

下图显示了ALLJOYN_SRP_KEYX AUTH机制消息流。这个身份验证机制主要设计用于其中通过双方产生的一次性使用密码的用例。

![alljoyn-srp-keyx-身份验证-机制][alljoyn-srp-keyx-auth-mechanism]

**Figure:** ALLJOYN_SRP_KEYX 身份验证机制

消息流的步骤如下所述。

1. T消费者应用程序生成一个28字节c_rand客户随机字符串。
2. 消费者(客户端)程序生成一个AuthChallenge METHOD_CALL消息并通过“AUTH ALLJOYN_SRP_KEYX & lt;c_rand>”参数信息。消费者应用程序发送的方法调用
提供者通过AllJoyn路由器(服务器)的应用。
3. 该供应商的应用程序调用由应用程序注册的要求输入密码验证侦听器回调。该AuthListener返回的密码。 “匿名”的用户名在这种情况下使用。
4. 该供应商的应用程序计算服务器的公共值B作为每个在RFC5054]第2.5.3算法（http://www.rfc-editor.org/rfc/rfc5054.txt）。
5. 该供应商的应用程序产生一个验证挑战METHOD_RETURN邮件发送服务器密钥交换消息给客户端。提供者的应用通过“DATA＆LT;N：摹S：B＆gt;”中作为参数传递给该消息。请参阅[RFC5054]第2.5.3（http://www.rfc-editor.org/rfc/rfc5054.txt）。'S'的是一个40字节的随机盐值。该供应商的应用程序的方法回复通过AllJoyn路由器消费者应用程序发送。
6. The consumer app validates the values of N, g, s and B per
section 2.5.3 of [RFC 5054](http://www.rfc-editor.org/rfc/rfc5054.txt).消费者应用验证N，G，S和B每值
[RFC5054]第2.5.3（http://www.rfc-editor.org/rfc/rfc5054.txt）。
7. 消费者应用程序计算每个客户的公共价值
2.5.4节(RFC 5054)(http://www.rfc-editor.org/rfc/rfc5054.txt)。
8. 消费者（客户端）的应用程序产生的验证挑战METHOD_CALL消息，并通过“数据＆LT; A＆gt;”参数信息。消费者应用程序发送方法调用通过AllJoyn路由器提供商（服务器）应用程序。
9.提供者应用程序生成一个28字节s_rand服务器随机字符串。
10. 该供应商的应用程序计算在RFC5054]第2.6条采用的算法预置密码（http://www.rfc-editor.org/rfc/rfc5054.txt）。
预置密码是基于其他参数间的客户端的公用值（A），服务器的公值（B）和密码。
11. 该供应商的应用程序基于所预置密码，c_rand主密钥，并按8.1节s_rand算法[RFC5246]（http://www.rfc-base.org/txt/rfc-5246.txt）。
12. 该供应商的应用程序计算“服务器完成”s_verifier按算法在RFC5246]第7.4.9（http://www.rfc-base.org/txt/rfc-5246.txt）。该s_verifier基于主密钥，握手消息的哈希值，而“系统完成”的标签生成的。
13.该供应商的应用程序产生的AuthChallenge METHOD_RETURN 消息并通过“数据＆LT; s_rand：s_verfier＆gt;”中作为该邮件的参数。该供应商的应用程序将通过AllJoyn路由器的方法答复消费者应用程序。
14. 消费者应用程序调用AuthListener回调注册应用程序要求输入密码。AuthListener返回密码。用户名“匿名”在这种情况下使用。
15. 消费者应用计算预置密码使用算法在2.6节(RFC5054)(http://www.rfc-editor.org/rfc/rfc5054.txt)。
预置密码是基于客户的公共价值(A),服务器的公共价值(B)和密码等参数。
16. 消费者应用程序计算基于预置密码,c_rand,s_rand按8.1节的算法(RFC 5246)(http://www.rfc - base.org/txt/rfc - 5246. - txt)。
17. 消费者应用程序生成“服务器完成”验证器使用相同的算法作为提供者应用程序和验证s_verifier收到的计算值是一样的。
18. 消费者应用程序计算出一个“客户端完成”c_verifier按算法在RFC5246]第7.4.9（http://www.rfc-base.org/txt/rfc-5246.txt）。
生成c_verifier基于主秘密,握手消息的哈希,“客户完成”标签。
19. 消费者应用程序生成一个AuthChallenge METHOD_CALL c_verifier消息发送到服务器。消费者应用程序通过“数据& lt;c_verifier>”方法调用的参数。消费者应用程序将方法调用发送到提供者(服务器)应用程序通过AllJoyn路由器。
20. 该供应商的应用程序产生了“客户端完成”验证，使用相同的算法作为消费者的应用并核实所计算的值是相同的收到的C验证。在这一点上，客户机和服务器已认证彼此。
21. 该供应商的应用程序产生，表明身份验证的AuthChallenge METHOD_RETURN的信息是完整的。该供应商应用程序通过“好& lt;s_全局唯一标识符>”参数信息,其中s_全局唯一标识符是身份验证提供者的全局唯一标识符应用。该供应商应用程序通过AllJoyn路由器发送方法回复给消费者应用程序。
22. 消费者应用程序发送一个AuthChallenge METHOD_CALL给供应商应用程序指定“BEGIN& lt;c_GUID>”参数。这表明供应商,客户已收到好的消息,以及数据流的消息即将开始。该c_GUID是消费者应用程序的权威性全局唯一标识符。
23. 提供者应用程序发送一个AuthChallenge METHOD_RETURN信息，指定“BEGIN”作为参数。

### ALLJOYN_SRP_LOGON

下图显示了ALLJOYN SRP_LOGON 身份验证机制消息流。 这种身份验证机制是专为客户机-服务器使用情况下,服务器维护用户名和密码,和客户端使用这些信息进行身份验证。这种机制非常类似于AllJoyn_SRP_KEYX身份验证机制与以下差异:

*消费者应用程序调用AuthListener从至应用程序请求用户名和密码。然后消费者应用程序在第一AuthChallenge消息通过用户名发送到供应商应用程序。
*供应商应用程序使用收到的用户名向AuthListener请求密码。
![alljoyn-srp-logon-auth-mechanism][alljoyn-srp-logon-auth-mechanism]

**Figure:** ALLJOYN_SRP_LOGON 身份验证机制

### ECDH密钥交换


在14.06版本中，新的椭圆曲线的Diffie-Hellman（ECDHE）基于身份验证机制短暂
加入。有关基于ECDHE的主验证机制的详细信息，请参阅最新版本的[安全HLD]的（https://wiki.allseenalliance.org/core/security_enhancements#high-level-design-documents）。

## 会话密钥的生成
下图显示了对等应用程序之间的会话密钥的生成消息流。

![session-key-generation-between-peer-apps][session-key-generation-between-peer-apps]

**图：** 对等应用程序之间会话密钥的生成
消息流的步骤如下所述。
1. 消费者应用程序生成一个28字节客户c_nonce nonce字符串。
2.消费者应用程序生成一个GenSessionKey METHOD_CALL消息并将其通过AllJoyn路由器发送给供应商应用程序。这个消息包括本地身份验证全局唯一标识符对应消费者应用,远程身份验证全局唯一标识符相应的提供者应用程序,和c_nonce。
3. 服务器供应商应用程序生成一个28字节s_nonce nonce字符串。
4. 供应商应用程序生成会话密钥,并验证基于主密钥,c_nonce,s_nonce 使用6.3节中描述的算法(RFC 5246)(http://www.rfc - base.org/txt/rfc - 5246. - txt)。
“会话密钥”的标签被用来生成密钥。
5. 供应商应用程序商店对等状态表中的会话密钥身份验证全局唯一标识符与消费者相关联的应用程序。
6.供应商应用程序生成一个128位组关键如果没有组密钥存在供应商应用程序,和商店的对等状态表。
7. 提供商应用程序产生一个GenSessionKey METHOD_RETURN消息并将其发送到经由AllJoyn路由器的消费者应用程序。
这个消息包括s_nonce和验证。
8. 消费者应用程序生成使用相同的算法作为提供应用程序的每节6.3基于主密钥，c_nonce和s_nonce的一个会话密钥和验证[RFC5246]（http://www.rfc-base.org/TXT / RFC-5246.txt）。
“会话密钥”的标签被用于生成密钥。  
9. 消费者应用验证所计算的验证是相同的接收到的验证。
10. 消费者应用程序商店与供应商的应用程序相关联的权威性GUID在对等状态表中的会话密钥。
11. 如果没有组密钥对消费者存在应用程序，并存储在对等体状态表中，消费者应用程序产生一个128位的群密钥。
对等应用程序现在具有可以用于交换加密消息的共同对话密钥。

## 组密钥的交换

下图显示了组密钥的交换消息流之间的对等的应用程序。这是通过ExchangeGroupKeys的方法调用会话密钥建立后对等应用程序之间传送的第一加密消息来实现的。

![group-keys-exchange][group-keys-exchange]

**图:** 组密钥的交换

消息流的步骤如下所述。

1. 消费者应用程序生成一个ExchangeGroupKeys METHOD_CALL消息。这个消息包括消费者应用的组密钥。消费者应用程序设置加密标志适用于这个消息。
2. 消费者应用程序加密信息并生成一个8字节的MAC(消息身份验证代码)使用远程对等应用的会话密钥。消息加密使用AES CCM算法。
3. 消费者应用附加MAC来加密消息体并更新消息长度来反映MAC。
4. 消费者应用程序通过AllJoyn路由器发送加密ExchangeGroupKeys METHOD_CALL消息提供者应用程序。
5. 提供商应用验证MAC以及使用存储为在消费者应用中的会话密钥解密消息。
6. 该供应商的应用程序商店中的对等状态表中远程对等方（消费者应用程序）接收组密钥。
7. 供应商应用程序生成一个ExchangeGroupKeys METHOD_RETURN消息。这个消息包括供应商应用程序的组密钥。供应商应用程序设置加密标志适用于这个消息。
8. 该供应商的应用程序加密消息，并使用远程对等应用程序会话密钥生成一个8字节的MAC。消息加密使用AES CCM算法完成。
9. 该供应商的应用程序附加到MAC加密邮件正文和更新信息的长度，以反映MAC。
10. 该供应商的应用程序发送加密的ExchangeGroupKeys METHOD_RETURN消息通过AllJoyn路由器供应商的应用程序。
11. 消费者应用验证MAC和使用存储在提供商的应用程序中的会话密钥解密答复消息。
12. 消费者应用程序商店中的对等状态表中远程对等（应用提供商）所接收到的组密钥。

现在这两个应用程序彼此都有组密钥，可以用来解密从对等应用程序接收到的广播信号消息。

## 交换加密消息

一旦应用程序之间建立加密证书,他们可以交换加密方法和信号。这些用例在下面抓获。

### 加密方法调用

下面的图显示了消费者和供应商应用程序中消息流之间的交换加密方法调用/答复。加密方法调用的应答消息也被加密发送。

![encrypted-method-call-reply][encrypted-method-call-reply]

**图:** 加密的方法调用/答复

消息流的步骤如下所述。

1. 消费者应用程序生成一个METHOD_CALL消息安全方法和设置加密标志以适用于这个消息。
2. 消费者应用程序加密信息并生成一个8字节的MAC使用目标应用程序的会话密钥。 消息加密使用AES CCM算法。
3. 消费者应用附加的MAC加密消息体并更新消息长度反映了MAC。
4. 消费者应用程序通过AllJoyn路由器发送加密METHOD_CALL消息给供应商应用程序。
5. 该供应商的应用程序验证MAC和解密使用存储在消费者应用程序中会话密钥的消息。
6. 该提供商应用程序的AllJoyn核心库调用MethodCall handler，它调用服务对象的接口上的方法调用并收到了答复。
7. 供应商应用程序生成一个METHOD_RETURN消息的应答并设置加密标志适用于这个消息。
8. 提供者应用程序使用消费者应用程序的会话密钥加密信息并生成一个8字节的MAC。 消息加密使用AES CCM算法。
9. 提供者应用附加的MAC加密消息体并更新消息长度反映了MAC。
10. 提供者应用程序通过AllJoyn路由器发送加密METHOD_RETURN消息给消费者应用程序。
11. 消费者应用程序验证MAC和使用供应商应用程序存储会话密钥以解密应答消息。
12. 消费者应用AllJoyn核心库发送纯文本回复消息给应用程序。

### 加密信号

下图显示了消息流从供应商应用程序到使用者应用程序发送一个加密的会话建立的信号。可以将该信号发送到目的地(单播信号)或多个端点多播/广播信号。

**注释:** 无会话信号不会在当前AllJoyn系统发送加密。在将来，实施也可以增强加密无会话的信号。

![encrypted-signal][encrypted-signal]

**图:** 加密的方法调用/答复

消息流的步骤如下所述。

1. 使用者和提供者应用程序已经互相验证,建立了加密密钥。
2. 提供者应用程序有一些信号数据需要发送。它调用BusObject信号()调用,生成一个SIGNAL信息。
3. 如果信号被定义为一个安全接口的一部分，提供商应用程序会设置加密标志对SINGAL消息适用。
4. 提供者应用程序加密信号信息,并生成一个8字节MAC使用组密钥或按以下密钥选择逻辑的会话密钥在(提供者应用程序)图。消息加密使用AES CCM算法。
5. 提供者应用附加MAC来加密SIGNAL消息体以及更新消息长度以反映MAC。
6. 该供应商的应用程序通过AllJoyn路由器发送加密SIGNAL信号消息给消费者应用程序。
7.  消费者应用程序验证MAC和解密SINGAL消息使用会话密钥或主要按以下密钥选择逻辑(消费者应用程序)图的组密钥。
8. 消费者应用AllJoyn核心库发送明文信号消息给应用程序。

#### 密钥选择逻辑

在供应商的应用程序方面，单播信号开始使用会话密钥和多播/广播信号使用群组密钥加密得到加密。下图显示了加密信号的密钥选择逻辑。

![key-selection-signal-encryption-provider-app][key-selection-signal-encryption-provider-app]

**图:** 信号加密的密钥选择（供应商的应用程序）

在消费者方面，下图中显示的反向逻辑被应用于用于选择密钥来解密所接收的信号的消息。
![key-selection-signal-decryption-consumer-app][key-selection-signal-decryption-consumer-app]

**图:** 解密信号的密钥选择(消费者应用程序)

## org.alljoyn.Bus.Peer.Authentication interface

org.alljoyn.Bus.Peer.Authentication interface 是AllJoyn接口在两个AllJoyn核心库中用于支持应用程序层安全的.

下表显示的是org.alljoyn.Bus.Peer.Authentication interface.的成员

#### org.alljoyn.Bus.Peer.Authentication interface methods

| 方法 | 描述 |
|---|---|
| ExchangeGuids | 应用程序与远程对等应用程序交换其认证全局唯一标识符和认证协议版本的方法。|
| AuthChallenge | 应用程序与远程对等应用程序启动验证和交换认证数据的方法。 |
| GenSessionKey | 应用程序生成一个应用程序与远程对等会话密钥的方法。 |
| ExchangeGroupKeys |  应用程序交换与远程对等应用程序组密钥的方法。|

#### org.alljoyn.Bus.Peer.Authentication.ExchangeGuids parameters

| 参数名称 | 方位 | 描述 |
|---|---|---|
| localGuid | in | 启动器应用程序的身份验证全局唯一标识符。 |
| localVersion | in | 启动器应用程序身份验证的版本。 |
| remoteGuid | out | 远程对等的应用程序身份验证全局唯一标识符。|
| remoteVersion | out |远程对等的应用程序身份验证的版本。 |

#### org.alljoyn.Bus.Peer.Authentication.AuthChallenge parameters

| 参数名称 | 方向 | 描述 |
|---|---|---|
| challenge | in | 发起者应用提供的身份验证数据 |
| response | out | 发起者应用返回的身份验证数据|

#### org.alljoyn.Bus.Peer.Authentication.GenSessionKey parameters

| 参数名称 | 方向 | 描述 |
|---|---|---|
| localGuid | in | 启动器应用程序的身份验证全局唯一标识符。 |
| remoteGuid | out | 远程对等的应用程序身份验证全局唯一标识符。 |
| localNonce | in |  由启动应用程序产生的随机数。|
| remoteNonce | out | 由远程对等应用程序产生的随机数。 |
| verifier | out | 远程对等应用生成的验证器。 |

#### org.alljoyn.Bus.Peer.Authentication.ExchangeGroupKeys parameters

| 参数名称 | 方向 | 描述 |
|---|---|---|
| localKeyMatter | in | 启动器应用程序的组密钥。 |
| remoteKeyMatter | out | 远程对等应用程序的组密钥。 |



[list-of-subjects]: /learn/core/system-description/


[alljoyn-security-arch]: /files/learn/system-desc/alljoyn-security-arch.png
[e2e-security-flow-two-unauth-apps]: /files/learn/system-desc/e2e-security-flow-two-unauth-apps.png
[authenticated-apps-reconnecting]: /files/learn/system-desc/authenticated-apps-reconnecting.png
[exchange-auth-guids]: /files/learn/system-desc/exchange-auth-guids.png
[alljoyn-rsa-keyx-auth-mechanism]: /files/learn/system-desc/alljoyn-rsa-keyx-auth-mechanism.png
[alljoyn-srp-keyx-auth-mechanism]: /files/learn/system-desc/alljoyn-srp-keyx-auth-mechanism.png
[alljoyn-srp-logon-auth-mechanism]: /files/learn/system-desc/alljoyn-srp-logon-auth-mechanism.png
[alljoyn-pin-keyx-auth-mechanism]: /files/learn/system-desc/alljoyn-pin-keyx-auth-mechanism.png
[session-key-generation-between-peer-apps]: /files/learn/system-desc/session-key-generation-between-peer-apps.png
[group-keys-exchange]: /files/learn/system-desc/group-keys-exchange.png
[encrypted-method-call-reply]: /files/learn/system-desc/encrypted-method-call-reply.png
[encrypted-signal]: /files/learn/system-desc/encrypted-signal.png
[key-selection-signal-encryption-provider-app]: /files/learn/system-desc/key-selection-signal-encryption-provider-app.png
[key-selection-signal-decryption-consumer-app]: /files/learn/system-desc/key-selection-signal-decryption-consumer-app.png
