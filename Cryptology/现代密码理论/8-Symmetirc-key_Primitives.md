## Stream Cipher

流密码

- RC4：安全问题大，不使用
- Trivium
- ChaCha20

## Block Ciphers

分组密码（注意，分组密码不是加密方案）

- DES：n=64bits，k=56bits
- AED：n=128bits，k=128,192,256bits

### 设计目标

分组密码应该表现得像随机排列

- n比特的数据，排列数量是$(2^n)! \approx n2^n$
- 使用简洁的描述（concise description，这里指短密钥）生成一系列排列
- 和PRP具有相似的安全属性

属性Properties

- 一个比特变了，应该影响所有比特

两种主要的方法：Substitution Permutation Network 和 Feistel Network

### Substitution Permutation Network

SPN  <= AES

雪崩效应，S盒中一个比特变化可以影响两个输出

任何一个S输出都会在下一轮中作为输入

### Feistel Network

### DES

16轮Feistel network

两轮DES安全性没有想象中高，2^63（meet-in-middle attack）

## Hash

MD5 不安全

SHA1

SHA2 - SHA-256 & SHA-512

SHA3: Keccak