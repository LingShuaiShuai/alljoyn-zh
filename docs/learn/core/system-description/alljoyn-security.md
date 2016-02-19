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

**NOTE:** The current implementation has a default TTL of 2 days
for session keys. If applications remain connected that long,
the associated session key expires and a new session key would
need to be generated.

### Group key

The group key is a cryptographic key used to encrypt
point-to-multipoint data traffic (broadcast signals) sent
out by a provider application. A single group key is maintained
by an application to encrypt broadcast signals sent to every
connected peer application.

A group key is generated when an application generates the
very first session key for any connected peer. The group key
is always generated independent of the provider or consumer
role of an application. Only provider applications use the
group key to send out encrypted broadcast signals. Applications
exchange their group keys using an encrypted method call that
involves the session key.  

The group key is 128 bits long and randomly generated.
The group key is directional in nature. Each application has
its own group key to encrypt broadcast signals. In addition,
it also maintain a separate peer group key for each of the
connected peer for decrypting broadcast signals received from them.

Group keys are stored in the memory, they are not persisted.
The group expires when the connection ends with peer
applications. An application's own group key will expire when
it no longer has any connections with any of its peers.
The group key for a remote peer will expire when the application
no longer has a connection with that peer.

**NOTE:** In future releases, group keys may be persisted to
support encryption for sessionless signals.

### Key store

The key store is a local storage used to persistently store
authentication-related keys, and to store master secret and
associated TTL. Applications can provide their own implementation
of the key store or use the default key store provided by the AllJoyn system.

Multiple applications on a device can share a given key store.
In this case, those applications share the same set of
authentication keys. In the current implementation, content
inside the key store is encrypted with a key derived from the key store path.

For every authenticated application, the key store maintains
the master secret and the associated TTL per Auth GUID of that
application. It also maintains the auth GUID assigned to the
local applications that are using the key store.

The following table shows an example key store with master
secret stored for two peer applications.

#### Key store example with master secret stored for two peer applications

**Local Auth GUID - GUIDx**

| Peer Auth GUID | Master secret | TTL |
|---|---|---|
| GUID1 | MS1 | T1 |
| GUID2 | MS2 | T2 |

## End-to-end security flow

The following figure captures the high-level end-to-end
message flow for AllJoyn security for the use case when
two applications have not authenticated with each other
before. The security message flow is initiated based on one
of the following triggers:

* Consumer app invoking a secure method call on a remote
service object or
* Consumer app explicitly invoking an API to secure the
connection with the remote peer

![e2e-security-flow-two-unauth-apps][e2e-security-flow-two-unauth-apps]

**Figure:** End-to-end security flow (two applications have not authenticated with each other before)

It is ideal for an application to always explicitly secure
the connection with the remote peer. In the case when an
application is just interested in receiving secure signals,
that is the only way to secure the connection with the
remote peer in order to receive keys for decrypting signals.

The AllJoyn core library attached with the application
implements all of the AllJoyn security logic. The AllJoyn
router only acts as a pass-through for security-related messages.
Each application needs to invoke EnablePeerSecurity API call
with the AllJoyn core library to enable AllJoyn security.
The application specifies authentication mechanism to be used,
the Auth Listener for callback and the key store file as part
of this API call. It also indicates whether key store can
be shared. The AllJoyn core library generates the auth GUID
for the application as part of first-time initialization of
the key store. The auth GUID gets stored in the key store.

After establishing a session with the provider app, the consumer
app initiates one of the security triggers mentioned above.
The AllJoyn Core library checks to see if authentication
is already in progress.

* If yes, it stops.
* If no, it continues with the security flow.

It looks for the key material for remote peer. In this case,
since this is the first secure interaction with the remote
app, no key material is found. This will trigger security
flow with the remote peer.

The message flow consists of following four distinct
steps in that order:

1. Exchange Auth GUIDs: This step involves exchanging Auth
GUIDs between peer applications. Once learned, the remote
app auth GUID is used to see if the master secret is present
for that auth GUID in the key store. In this case, no master
secret is found since the two apps have not authenticated
with each other.
2. App-to-App authentication: This step involves two peers
authenticating each other using one of the supported auth
mechanisms. At the end of this step, two peers have authenticated
each other and now share a common master key.
3. Generate a session key: This step involves two peers
generating a session key to be used for encrypting secure
point-to-point messages between them. The session key is
generated independently by both the peers based on the
shared master key. A group key is also generated when the
first session key is generated.
4. Exchange group keys: This step involves two peers exchanging
their own group keys with each other via an encrypted
AllJoyn message. The AllJoyn message gets encrypted using
the session key already established between two peers.
The group key is used by the application to encrypt session
multicast and broadcast signals. At the end of this step,
two peer applications have group keys to decrypt secure
broadcast signals received from each other.

Details for each of these steps are described in the
following sections. After completing these steps, peer
applications have now established encryption/decryption
keys to exchange encrypted method calls and signals.

These keys are managed as part of a peer state table
which includes a unique name for the remote peer, as well
as a local auth GUID and group key for the current application.

The following table provides a sample peer state table with
keys stored for two authenticated peer applications.

#### Sample peer state table for two authenticated peer applications.

**Local Auth GUID - GUIDx**
**App Group Key - GKx**

| Peer Auth GUID | Unique name | Session key | Peer group key |
|---|---|---|---|
| GUID1 | :100.2 | SK1 | GK1 |
| GUID2 | :200.2 | SK2 | GK2 |

## Already authenticated apps

When apps connect with each other subsequent to their first
connection, they do not need to authenticate again with each
other if the master secret is still valid. The following figure
shows security flow for this use case.

![authenticated-apps-reconnecting][authenticated-apps-reconnecting]

**Figure:** Authenticated apps connecting again

In this case, since the apps were not connected, no key material
is found for the remote peer. As a result, the consumer app
performs the Exchange Auth GUIDs step with the remote peer.
This retrieves the Auth GUID for the remote peer that can
be used for lookup in the key store file.

Since the apps have already authenticated, a master secret
is found in the key store for the auth GUID of the provider
app and no app-to-app authentication needs to occur.
The consumer app directly goes to the next step of generating
session key and/or group key.

**NOTE:** If the verification step fails during session key
generation, the consumer app must re-authenticate with
the provider even if master secret is still valid.

## Exchange of Auth GUIDs

The following figure shows the message flow for the exchange
of Auth GUIDs between peer applications.

![exchange-auth-guids][exchange-auth-guids]

**Figure:** Exchange of Auth GUIDs

The message flow steps are described below.

1. The consumer app generates an ExchangeGuids METHOD_CALL
message and sends it to the provider app via the AllJoyn router.
This message includes the Auth GUID of the consumer app and
the maximum Auth version supported by the consumer app.
2. The provider app proposes its max auth version if it
does not support the auth version received from the consumer app.
3. The provider app generates an ExchangeGuids METHOD_RETURN
message and sends it to the consumer app via the AllJoyn router.
This message includes the Auth GUID of the provider app and
the max auth version of the provider app.
4. The consumer app verifies that it supports the received
auth version. This completes the Exchange GUIDs step.

## App-to-app authentication

AllJoyn peer applications authenticate each other using
one of the auth mechanisms detailed in this section.
These auth mechanisms are designed based on the security
constructs in [RFC 5246](http://www.rfc-base.org/txt/rfc-5246.txt) and
[RFC 5054](http://www.rfc-editor.org/rfc/rfc5054.txt). Applicable RFC
sections are listed when describing details for these auth mechanisms.

**NOTE:** For the authentication message flows captured in this section,
the consumer and provider apps are also referred to as client
and server respectively, to correspond with terminology used
in [RFC 5246](http://www.rfc-base.org/txt/rfc-5246.txt) and
[RFC 5054](http://www.rfc-editor.org/rfc/rfc5054.txt).

### Use of D-Bus SASL protocol

The AllJoyn framework implements the D-Bus SASL exchange protocol
[D-Bus Specification](http://dbus.freedesktop.org/doc/dbus-specification.html) to exchange
authentication-related data. All authentication-related
exchanges are done using the AuthChallenge method call/reply
defined as part of the org.alljoyn.Bus.Peer.Authentication
interface implemented by the AllJoyn core library.

Auth data to be exchanged is generated as a SASL string
based on the D-Bus SASL exchange protocol. Auth data inside
the SASL string is sent in the hex form. The generated string
is then passed as a parameter to the AuthChallenge method call
or method reply.

For example, to initiate authentication for ALLJOYN_SRP_KEYX,
generated string would be:

```
"AUTH ALLJOYN_SRP_KEYX <c_rand in hex>"
```

This includes the SASL AUTH command, auth mechanism, and auth data in hex form.

The following table captures the D-Bus SASL commands supported
by the AllJoyn framework.

#### D-Bus SASL commands supported by the AllJoyn framework

| Command | Direction | Description |
|---|---|---|
| AUTH [mechanism] [initial-response] | Consumer->Provider | Start the authentication. |
| CANCEL | Consumer->Provider | Cancel the authentication. |
| BEGIN | <ul><li>Consumer->Provider</li><li>Provider->Consumer</li></ul> | <ul><li>On the consumer side, acknowledge that the consumer has received an OK command from the provider, and that the stream of messages is about to begin.</li><li>From the provider side, sent by the provider as response to BEGIN command from the consumer.</li></ul> |
| DATA | <ul><li>Consumer->Provider</li><li>Provider->Consumer</li></ul> | On the consumer or provider side, contains a hex-encoded block of data to be interpreted according to the auth mechanism in use. |
| OK | Consumer->Provider  | The client has been authenticated. |
| REJECTED | Consumer->Provider | On the consumer side, indicates that the current authentication exchange has failed, and further exchange of DATA is inappropriate. The consumer tries another mechanism, or tries providing different responses to challenges. |
| ERROR | <ul><li>Consumer->Provider</li><li>Provider->Consumer</li></ul> | On the consumer or provider side, either the provider or consumer did not know a command, does not accept the given command in the current context, or did not understand the arguments to the command. |

### ALLJOYN_SRP_KEYX

The following figure shows the message flow for the ALLJOYN_SRP_KEYX
auth mechanism. This auth mechanism is primarily designed for use
cases where a one-time use password is generated by both sides.

![alljoyn-srp-keyx-auth-mechanism][alljoyn-srp-keyx-auth-mechanism]

**Figure:** ALLJOYN_SRP_KEYX auth mechanism

The message flow steps are described below.

1. The consumer app generates a 28 bytes client random string c_rand.
2. The consumer (client) app generates an AuthChallenge METHOD_CALL
message and passes "AUTH ALLJOYN_SRP_KEYX &lt;c_rand&gt;" as parameter
in that message. The consumer app sends the method call to the
provider (server) app via the AllJoyn router.
3. The provider app invokes the AuthListener callback registered
by the application to request for a password. The AuthListener
returns the password. A username of "anonymous" is used in this case.
4. The provider app computes the server's public value B as
per the algorithm in section 2.5.3 of [RFC 5054](http://www.rfc-editor.org/rfc/rfc5054.txt).
5. The provider app generates an AuthChallenge METHOD_RETURN
message to send a server key exchange message to the client.
The provider app passes "DATA &lt;N:g:s:B&gt;" as parameter to that
message. Refer to section 2.5.3 of [RFC 5054](http://www.rfc-editor.org/rfc/rfc5054.txt).
The 's' is a 40 bytes random salt value. The provider app
sends method reply to the consumer app via the AllJoyn router.
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
