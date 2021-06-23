## Key Management

如果用户不能私下见面

公钥密码学基于密钥的安全分发

## 密钥分发中心

一些服务器（Key Distribution Center，KDC，密钥分发中心）将密钥分发给用户

- 如果用户都在同一家公司（使用相同KDC）
- 用户只使用KDC分享密钥
- KDC在用户开始通信时生成新的密钥（Session keys）

坏处：

- 在因特网上是不可行的
- 依赖于KDC的诚实
- 谁能实现一个令人信赖的KDC
- KDC需要永久可用
- KDC会单点失败

### 和服务器建立联系

A Alice，Alice和服务器共享密钥 $K_{AS}$

服务器 Server S

B Bob，Bob和服务器共享密钥$K_{BS}$

${\{M\}}_K$ 一条通过密钥K进行认证加密（AE）的消息M

- 可以使用任何认证加密
- K=(K0,K1)一个密钥用于加密，一个用于认证
- 最好是先加密在认证 Encrypt-then-MAC

**Idea1：**

![image-20210622141207042](image/image-20210622141207042.png)

威胁模型：攻击者只是窃听 Eavesdropping

将Alice，Bob间的A改为Eve，攻击者自己的名字

![image-20210622141345956](image/image-20210622141345956.png)

**Idea2：**

![image-20210622141447962](image/image-20210622141447962.png)

**Idea3：**

使用Nonce（Number used once）来防止重放攻击

由一方生成，然后返回给他，以此表示消息是新生成的

![image-20210622141654439](image/image-20210622141654439.png)

攻击者可以使用一个旧的密钥

通过时间戳来证明密钥的时效

![image-20210622141759556](image/image-20210622141759556.png)



## Public-Key Cryptograph

Key Exchange

分析、证明
