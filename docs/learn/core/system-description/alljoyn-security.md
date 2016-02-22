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
3.该供应商的应用程序调用由应用程序注册的要求输入密码验证侦听器回调。该AuthListener返回的密码。 “匿名”的用户名在这种情况下使用。
4.该供应商的应用程序计算服务器的公共值B作为每个在RFC5054]第2.5.3算法（http://www.rfc-editor.org/rfc/rfc5054.txt）。
5. 该供应商的应用程序产生一个验证挑战METHOD_RETURN邮件发送服务器密钥交换消息给客户端。提供者的应用通过“DATA＆LT;N：摹S：B＆gt;”中作为参数传递给该消息。请参阅[RFC5054]第2.5.3（http://www.rfc-editor.org/rfc/rfc5054.txt）。'S'的是一个40字节的随机盐值。该供应商的应用程序的方法回复通过AllJoyn路由器消费者应用程序发送。
6. The consumer app validates the values of N, g, s and B per
section 2.5.3 of [RFC 5054](http://www.rfc-editor.org/rfc/rfc5054.txt).
7. The consumer app computes the client's public value A per
section 2.5.4 of [RFC 5054](http://www.rfc-editor.org/rfc/rfc5054.txt).
8. The consumer (client) app generates an AuthChallenge METHOD_CALL
message and passes "DATA &lt;A&gt;" as parameter in that message.
The consumer app sends the method call to the provider (server)
app via the AllJoyn router.
9. The provider app generates a 28 bytes server random string s_rand.
10. The provider app computes a premaster secret using the
algorithm in section 2.6 of [RFC 5054](http://www.rfc-editor.org/rfc/rfc5054.txt).
The premaster secret is based on the client's public value (A),
the server's public value (B), and password among other parameters.
11. The provider app computes a master secret based on
the premaster secret, c_rand, and s_rand as per the algorithm
in section 8.1 of [RFC 5246](http://www.rfc-base.org/txt/rfc-5246.txt).
12. The provider app computes a "server finish" s_verifier as
per the algorithm in section 7.4.9 of [RFC 5246](http://www.rfc-base.org/txt/rfc-5246.txt).  
The s_verifier is generated based on master secret, hash of
handshake messages, and "server finish" label.
13. The provider app generates an AuthChallenge METHOD_RETURN
message and passes "DATA &lt;s_rand:s_verfier&gt;" as parameter
to that message. The provider app sends the method reply
to the consumer app via the AllJoyn router.
14. The consumer app invokes the AuthListener callback
registered by the application to request for a password.
The AuthListener returns the password. A username of "anonymous"
is used in this case.
15. The consumer app computes a premaster secret using the
algorithm in section 2.6 of [RFC 5054](http://www.rfc-editor.org/rfc/rfc5054.txt).
The premaster secret is based on the client's public value (A),
the server's public value (B), and the password among other parameters.
16. The consumer app computes a master secret based on the
premaster secret, c_rand, and s_rand as per the algorithm
in section 8.1 of [RFC 5246](http://www.rfc-base.org/txt/rfc-5246.txt).
17. The consumer app generates the "server finish" verifier
using the same algorithm as the provider app and verifies
that the computed value is same as the received s_verifier.
18. The consumer app computes a "client finish" c_verifier
as per the algorithm in section 7.4.9 of [RFC 5246](http://www.rfc-base.org/txt/rfc-5246.txt).
The c_verifier is generated based on the master secret,
hash of handshake messages, and "client finish" label.
19. The consumer app generates an AuthChallenge METHOD_CALL
message to send the c_verifier to the server. The consumer app
passes "DATA &lt;c_verifier&gt;" as parameter to the method call.
The consumer app sends the method call to the provider (server)
app via the AllJoyn router.
20. The provider app generates the "client finish" verifier
using the same algorithm as the consumer app and verifies
that the computed value is same as the received c_verifier.
   At this point, the client and server have authenticated with each other.
21. The provider app generates an AuthChallenge METHOD_RETURN
message indicating that authentication is complete. The provider
app passes "OK &lt;s_GUID&gt;" as parameter in that message, where s_GUID
is the auth GUID of the provider app. The provider app sends
the method reply to the consumer app via the AllJoyn router.
22. The consumer app sends an AuthChallenge METHOD_CALL to t
he provider app specifying "BEGIN &lt;c_GUID&gt;" as parameter.
This indicates to the provider that the client has received
the OK message, and the stream of data messages is about to begin.
The c_GUID is auth GUID of the consumer app.
23. The provider app sends an AuthChallenge METHOD_RETURN
message, specifying "BEGIN" as parameter.

### ALLJOYN_SRP_LOGON

The following figure shows the message flow for the ALLJOYN_SRP_LOGON
auth mechanism. This auth mechanism is designed for client-server use
cases where server maintains username and password, and the client
uses this information for authentication. This mechanism is quite
similar to the AllJoyn_SRP_KEYX auth mechanism with the following differences:

* The consumer app invokes the AuthListener callback up front
to request the username and password from the application.
The consumer app then passes the username in the first AuthChallenge
message sent to the provider app.
* The provider app uses the received username to request for
password from the AuthListener.

![alljoyn-srp-logon-auth-mechanism][alljoyn-srp-logon-auth-mechanism]

**Figure:** ALLJOYN_SRP_LOGON auth mechanism

### ECDHE key exchanges

In the 14.06 release, new Elliptic Curve Diffie-Hellman Ephemeral
(ECDHE) based auth mechanism were added. For details on ECDHE-based
auth mechanisms, see the latest version of the [Security HLD](https://wiki.allseenalliance.org/core/security_enhancements#high-level-design-documents).

## Generation of the session key
The follwing figure shows the message flow for the generation
of session keys between peer applications.

![session-key-generation-between-peer-apps][session-key-generation-between-peer-apps]

**Figure:** Session key generation between peer applications

The message flow steps are described below.
1. The consumer app generates a 28 bytes client nonce string c_nonce.
2. The consumer app generates a GenSessionKey METHOD_CALL
message and sends it to the provider app via the AllJoyn router.
This message includes local auth GUID corresponding to the
consumer app, a remote auth GUID corresponding to the provider app, and c_nonce.
3. The provider app generates a 28 bytes server nonce string s_nonce.
4. The provider app generates a session key and a verifier
based on the master secret, c_nonce, and s_nonce using the
algorithm described in section 6.3 of [RFC 5246](http://www.rfc-base.org/txt/rfc-5246.txt).
The "session key" label is used to generate the key.
5. The provider app stores the session key in the peer state
table for the auth GUID associated with the consumer app.
6. The provider app generates a 128 bit group key if no group
key exists for the provider app, and stores in the peer state table.
7. The provider app generates a GenSessionKey METHOD_RETURN
message and sends it to the consumer app via the AllJoyn router.
This message includes s_nonce and verifier.
8. The consumer app generates a session key and a verifier
based on the master secret, c_nonce, and s_nonce using the
same algorithm as the provider app as per section 6.3 of [RFC 5246](http://www.rfc-base.org/txt/rfc-5246.txt).
The "session key" label is used for generate the key.  
9. The consumer app verifies that the computed verifier is
the same as the received verifier.
10. The consumer app stores the session key in the peer state
table for the auth GUID associated with the provider app.
11. The consumer app generates a 128 bit group key if no group
key exists for the consumer app, and stores in the peer state table.

The peer apps now have a common session key that can be used to
exchange encrypted messages.

## Exchange of group keys

The following figure shows the message flow for the exchange
of group keys between peer applications. This is achieved via
the ExchangeGroupKeys method call which is the first encrypted
message sent between peer applications after the session key is established.

![group-keys-exchange][group-keys-exchange]

**Figure:** Exchange of group keys

The message flow steps are described below.

1. The consumer app generates an ExchangeGroupKeys METHOD_CALL
message. This message includes the group key of the consumer app.
The consumer app sets the encryption flag to true for this message.
2. The consumer app encrypts the message and generates an 8 bytes
MAC (Message Authentication Code) using the session key for the
remote peer app. Message encryption is done using AES CCM algorithm.
3. The consumer app appends the MAC to the encrypted message body
and updates the message length to reflect the MAC.
4. The consumer app sends the encrypted ExchangeGroupKeys METHOD_CALL
message to the provider app via the AllJoyn router.
5. The provider app verifies the MAC and decrypts the message using the
session key stored for the consumer app.
6. The provider app stores the received group key for the remote peer
(consumer app) in the peer state table.
7. The provider app generates an ExchangeGroupKeys METHOD_RETURN message. This message includes the group key of the provider app. The provider app sets the encryption flag to true for this message.
8. The provider app encrypts the message and generates an
8 bytes MAC using the session key for the remote peer app.
Message encryption is done using AES CCM algorithm.
9. The provider app appends the MAC to the encrypted message
body and updates the message length to reflect the MAC.
10. The provider app sends the encrypted ExchangeGroupKeys
METHOD_RETURN message to the provider app via the AllJoyn router.
11. The consumer app verifies the MAC and decrypts the reply
message using session key stored for the provider app.
12. The consumer app stores the received group key for the
remote peer (provider app) in the peer state table.

Now the two apps have group key for each other which can be
used to decrypt broadcast signal messages received from the peer application.

## Exchange of encrypted messages

Once encryption credentials are established between applications,
they can exchange encrypted methods and signals. These use cases
are captured below.

### Encrypted method call

The following figure shows the message flow for exchange of
encrypted method call/reply between the consumer and provider
applications. The reply message to an encrypted method call
is also sent encrypted.

![encrypted-method-call-reply][encrypted-method-call-reply]

**Figure:** Encrypted method call/reply

The message flow steps are described below.

1. The consumer app generates a METHOD_CALL message for the
secure method and sets the encryption flag to true for this message.
2. The consumer app encrypts the message and generates an
8 bytes MAC using the session key for the destination app.
Message encryption is done using AES CCM algorithm.
3. The consumer app appends the MAC to the encrypted message
body and updates the message length to reflect the MAC.
4. The consumer app sends the encrypted METHOD_CALL message
to the provider app via the AllJoyn router.
5. The provider app verifies the MAC and decrypts the message
using session key stored for the consumer app.
6. The provider app's AllJoyn core library invokes the MethodCall
handler, which invokes the method call on the service object
interface and receives a reply.
7. The provider app generates a METHOD_RETURN message for
the reply and sets the encryption flag to true for this message.
8. The provider app encrypts the message and generates an
8 bytes MAC using the session key for the consumer app.
Message encryption is done using AES CCM algorithm.
9. The provider app appends the MAC to the encrypted message
body and updates the message length to reflect the MAC.
10. The provider app sends the encrypted METHOD_RETURN message
to the consumer app via the AllJoyn router.
11. The consumer app verifies the MAC and decrypts the reply
message using session key stored for the provider app.
12. The consumer app's AllJoyn core library sends the plaintext
reply message to the application.

### Encrypted signal

The following figure shows the message flow for sending an
encrypted session based signal from provider application
to consumer applications. The signal can be sent to a destination
(unicast signal) or to multiple endpoints as multicast/broadcast signals.

**NOTE:** Sessionless signals are not sent encrypted in current
AllJoyn system. In future, implementation can be enhanced to
encrypt sessionless signals as well.

![encrypted-signal][encrypted-signal]

**Figure:** Encrypted method call/reply

The message flow steps are described below.

1. The consumer and provider apps have already authenticated
and established encryption keys with each other.
2. The provider app has some signal data to send. It invokes
the BusObject Signal() call which generates a SIGNAL message.
3. The provider app sets the encryption flag to true for the
SIGNAL message if the signal is defined as part of a secure interface.
4. The provider app encrypts the SIGNAL message and generates
an 8 bytes MAC using either the group key or session key as
per the logic in the following key selection logic (provider app) figure.
Message encryption is done using AES CCM algorithm.
5. The provider app appends the MAC to the encrypted SIGNAL
message body and updates the message length to reflect the MAC.
6. The provider app sends the encrypted SIGNAL message to
the consumer app via the AllJoyn router.
7. The consumer app verifies the MAC and decrypts the SIGNAL
message using either the session key or group key as per the
logic in the following key selection logic (consumer app) figure.
8. The consumer app's AllJoyn core library sends the plaintext
signal message to the application.

#### Key selection logic

On the provider application side, unicast signals get encrypted
using the session key and multicast/broadcast signals get
encrypted using group key. The following figure shows the key selection
logic for encrypting signals.

![key-selection-signal-encryption-provider-app][key-selection-signal-encryption-provider-app]

**Figure:** Key selection for signal encryption (on the provider app)

On the consumer side, a reverse logic is applied for selecting
key for decrypting received signals messages as shown in the following figure.

![key-selection-signal-decryption-consumer-app][key-selection-signal-decryption-consumer-app]

**Figure:** Key selection for signal decryption (on the consumer app)

## org.alljoyn.Bus.Peer.Authentication interface

The org.alljoyn.Bus.Peer.Authentication interface is the AllJoyn
interface between two AllJoyn core libraries that support the
application layer security within AllJoyn.

The following table summarizes members from org.alljoyn.Bus.Peer.Authentication interface.

#### org.alljoyn.Bus.Peer.Authentication interface methods

| Method | Description |
|---|---|
| ExchangeGuids | Method for an application to exchange its auth GUID and authentication protocol version with a remote peer application. |
| AuthChallenge | Method for an application to initiate authentication and exchange authentication data with a remote peer application. |
| GenSessionKey | Method for an application to generate a session key with a remote peer application. |
| ExchangeGroupKeys | Method for an application to exchange group key with a remote peer application. |

#### org.alljoyn.Bus.Peer.Authentication.ExchangeGuids parameters

| Parameter name | Direction | Description |
|---|---|---|
| localGuid | in | Auth GUID for the initiator application. |
| localVersion | in | Auth version for the initiator application. |
| remoteGuid | out | Auth GUID for the remote peer application. |
| remoteVersion | out | Auth version for the remote peer application. |

#### org.alljoyn.Bus.Peer.Authentication.AuthChallenge parameters

| Parameter name | Direction | Description |
|---|---|---|
| challenge | in | Auth data provided by the initiator app. |
| response | out | Auth data returned by the provider app. |

#### org.alljoyn.Bus.Peer.Authentication.GenSessionKey parameters

| Parameter name | Direction | Description |
|---|---|---|
| localGuid | in | Auth GUID for the initiator application. |
| remoteGuid | out | Auth GUID for the remote peer application. |
| localNonce | in | Nonce generated by the initiator app. |
| remoteNonce | out | Nonce generated by the remote peer app. |
| verifier | out | Verifier generated by the remote peer app. |

#### org.alljoyn.Bus.Peer.Authentication.ExchangeGroupKeys parameters

| Parameter name | Direction | Description |
|---|---|---|
| localKeyMatter | in | Group key of the initiator app. |
| remoteKeyMatter | out | Group key of the remote peer app. |



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
