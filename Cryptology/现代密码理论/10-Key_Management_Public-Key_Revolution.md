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

## Public-Key Cryptograph

Key Exchange

