# 1-Introduction

## 私钥加密

- Classical terminology (术语): Codes / Ciphers
- Modern terminology: Encryption Scheme

双方共享一个密钥(对称设置，Symmetric Setting)，需要在监视所有通信的敌手的信道中秘密通信。

在可能被盗窃 (stolen) 或 hacked 的地方存储数据。

## The Syntax of Enc 一些规范

$K$: keyspace, 密钥空间

$M$: Plaintext space, 明文空间, 以前一般是字母，现在来说，是二进制字符串 binary string $\subset {\{0, 1}\}^*$

$C$: Cipertest space, 密文空间



### 加密方案 Encryption scheme (Gen; Enc; Dec)

1. $Gen \rightarrow K$ (probabilistic) 密钥生成算法 
2. $Enc: K \times M \rightarrow C$ 加密算法 Encryption
3. $Dec: K \times C \rightarrow M$ 解密算法 Decryption

正确性Correctness：$\forall k \in K, m \in M, Dec_k(Enc_k(m))=m$

定义安全更加复杂、困难。

## Kerckhoffs 准则

密码方案不能够需要保密，而且敌手可以随意获得。唯一需要保密的是密钥$k$，而非$Gen, Enc, Dec$

1. 保证密码方案是保密的是不现实的
2. 较短的密钥更容易保护，生成，替换
3. 密码方案设计需要公开讨论、分析

## Sufficient key-space 准则

足够的密钥空间

否则会被穷举攻击或者暴力法（尝试所有可能的密钥 brute-force / exhaustive-search)

## 现代密码学的准则

基于可证明安全 provable security (reductionist security 归约安全)

### 形式化安全定义

对安全目标security goal 和威胁模型 threat model 进行规范定义 formal definition

Security definition = Security goal + Threat model

Security goal:

- 敌手已知密文，Adversary is given $c=Enc_k(m)$
- 密文不应该泄露关于明文的额外信息

Threat model:敌手的能力，并不指定敌手的策略

此处参考[顾凌峰的知乎回答](https://www.zhihu.com/question/34624915)

- Ciphertest-text only attack 唯密文攻击：敌手只知道一些密文
- Known-plaintext attack 已知明文攻击: 敌手知道密文/明文对，目标是推断出使用同一密钥产生的其他密文的信息
- Chosen-plaintext attack 选择明文攻击：敌手任意选择一些密文明文对，还可以创造明文，获得其密文。
- Chosen-ciphertext attack 选择密文攻击：在已知明文的基础中，可以选择或制造密文，得到其明文。
- Chosen-text attack 选择文本攻击：选择明文与选择密文的和。

不同的加密方法有不同强度，选取合适的威胁模型。

### 明确假设

1. P vs. NP 找到一个解决方案比验证更难
2. 几乎所有密码方案基于 $P \neq NP$
3. 需要一些困难问题 "hardness assumption"（比如公钥密码学中大整数分解）

为什么需要明确假设

1. 通过数学证明安全性
2. 对假设的验证：可以对假设进行分析，与别的假设进行比较
3. 对加密方法的比较：在其他条件相同的情况下，选择基于更弱假设的方法。
4. 假设的必要性：如果在一个模块中发现了弱点，方案是否仍然安全
5. 模块化Modularity：由简单的模块构建方案，如果模块存在不足，容易替换

为什么不简单地假设方案本身是安全的？

- 启发式证明 heuristic proof: 提出一个方案，可以抵挡所有已知的攻击
- 攻击者如果攻破，可以不公布，由此获利

### 证明安全性

proof方案在假设下达到目标

保证方案在假设的困难问题和威胁模型中，可以达到安全目标。

并非不能攻击，需要分析方案的定义、假设。