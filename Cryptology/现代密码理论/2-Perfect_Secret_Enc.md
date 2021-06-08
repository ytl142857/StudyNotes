# 2-Perfect Secret Encryption

完善保密性

即使是面对有无限计算能力的敌手，也是安全的。

## 随机数生成与密钥生成

高熵High-entropy vs. 一致Uniform vs. 随机Random

在课程中，假设可以无限提供独立、无偏（unbiased）的比特串

现代随机数生成器有两步：

1.  收集高熵数据 Collect high-entropy data
2. 生成几乎独立无偏的比特序列 Yield a sequence of nearly independent and unbiased bits

## Perfect Secrecy

敌手可以窃听双方的通信，从而获得密文。

完善保密性意味着得到明文不能使敌手得到发送的密文的信息。

**Definition 2.3** 加密方案$(Gen; Enc; Dec)$是完善保密性的 perfectly secret, 如果对于所有分布distribution M，$\forall m \in M, c\in C$，有
  $Pr[M=m|C=c]=Pr[M=m]$

即：给定密文，消息的分布不变，也就是C和M互相独立

**Lemma 2.4** 加密方案$(Gen; Enc; Dec)$是完善保密性的 perfectly secret, 如果对于$\forall m,m' \in M$和$c \in C$，有
  $Pr[Enc_k(m)=c] = Pr[Enc_k(m')=c]$

对于**引理2.4**的证明：

1. 如果方案是完善保密性的，则引理2.4成立
2. 如果引理2.4成立，则方案是完善保密性的

## Indistinguishability

不可区分性，基于一个实验，敌手被动观察密文，猜测两条消息那一条加密了。

**Definition 2.5** 加密方案$\Pi=(Gen; Enc; Dec)$是完美不可区分Perfectly indistinguishable，如果对于$\forall A$有
  $Pr[Priv_{A, \Pi}^{eav}=1]=\frac{1}{2}$

**Lemma 2.6** $\Pi$是完善保密性，当且仅当他是完善不可区分

## 一个完善保密性的方案：一次一密

The One-Time Pad

参数Parameter $t \in N, M=K=C={\{0,1\}}^t$

- Gen 随机均匀输出 $k \in K$ 
- $Enc_k(m) = m \oplus k$
- $Dec_k(m) = c \oplus k$

异或 $\oplus$: 相同为0，不同为1

正确性：$Dec_k(Enc_k(m)) = k \oplus k \oplus m = m$

**Theorem 2.9** 一次一密方案是完善保密性的

实用性：

1. 密钥与加密的消息要一样长
2. 一次一密的密钥不能重用（如果重用，则不能抵挡已知明文攻击）

## 完善保密性的局限性

**Theorem 2.10** 如果 $Enc: K \times M \rightarrow C, Dec: K \times C \rightarrow M$是完善保密性的，则$|K| \geq |M|$

## Todo

Proof of:

- [ ] Definition 2.3
- [ ] Lemma 2.4
- [ ] Definition 2.5
- [ ] Lemma 2.6
- [ ] Theorem 2.9