# 3-Computational Security

计算安全性

- 目标：在合理的计算能力下不能以合理的概率攻破

- 两个放松理论信息要求的方式
  1. 只对于计算能力有限的敌手
  2. 在极小的概率下可以不安全

- 两种达到可计算安全的方式
  1. 精确方法Concrete approach：讨论具体实例化的安全性
  2. 渐进方法Asymptotic approach：使用计算复杂性理论的思想

## 精确方法

Concrete approach

通过显式地限定运行特定时间的敌手的最大成功概率来对密码方案的安全性进行量化。

可以通过这样来定义：

一个方案是$(t, \epsilon)-secure$ 的，如果任何敌手在最多 $t$ 执行时间内成功概率达到 $\epsilon$

在实践中，精确方法很重要，但是很难保证安全，而且一些精确方法仅限于现在，在未来也会失去安全性。

- 限定的运行时间不具有鲁棒性（robust）
- 依赖于底层和硬件的条件，可能随时间变化
- 未考虑并行或者其他计算模式

## 渐进方法

Asymptotic approach

### 基于计算复杂性理论

- 安全参数 $n \in N$，可以将n看做密钥长度
- 诚实方在多项式时间（probabilistic polynomial time, PPT）内运行

**Definition 3.4** A function $f:N \rightarrow R$ 是可忽略的（negligible），如果它比 inverse polynomial 下降得快

  $\forall polynomial \quad p(\cdot), \exist N, s.t. \forall n >N:f(n)<\frac{1}{p(n)}$

e.g. $f(n)=2^{-n};f(n)=2^{-\sqrt{n}};f(n)=n^{-nlogn}$

**Proposition 3.6** 当$negl_1,negl_2$都是可忽略函数时，$negl_3 = negl_1 + negl_2$也是可忽略函数，对任意多项式$p$，$negl_4=p(n)\cdot negl_1$也是可忽略函数

### 渐进式安全

一个密码方案是安全的如果任意多项式时间敌手成功概率最高是可忽略的。

对于诚实方（Honest Parties）运行时间是多项式时间的；对于多项式时间敌手来说方案需要安全。

### Strong Church-Turing Thesis

除了量子计算机，所有物理上可实现的计算都可以在图灵机上以最多多项式的时间进行模拟。

有效且安全的渐进定义有闭包性质

1. 对有界多项式的$p(n),q(n)$，$p(n)q(n), q(p(n))$也是多项式有界的
2. $p(n),q(n)$是可忽略的，$p(n)q(n), q(p(n))$也是可忽略的

### 定义 Definition

- 一个密码方案是安全的，如果对任何多项式时间敌手 $A$ 使用特定方法进行攻击，他成功的概率是可忽略的（negligible）

如果考虑 n 的选择

- 一个密码方案是安全的，如果对任何多项式时间敌手 $A$ 使用特定方法进行攻击，如果对任何多项式 $p$ ，存在一个整数 $N$，当 $n>N$时，敌手 $A$ 成功的概率小于 $\frac{1}{p(n)}$

### 一个典型的渐进安全性声明

构造的方案 $\Pi$ 是安全的当其依赖的假设和构成块（building block）$\pi$ 是安全的。

- 对所有 $p\in poly, f\in negl$，存在 $p'\in poly, f'\in negl$，$s.t.$如果没有 $p(n)$的敌手可以在$f(n)$内有效攻破（break）$\pi(n)$，则没有 $p'(n)$的敌手可以在$f'(n)$内有效攻破（break）$\Pi$
- 对所有 $p\in poly, f\in negl$，存在 $p'\in poly, f'\in negl$，如果有 $p'(n)$的敌手可以在$f'(n)$内有效攻破（break）$\Pi$，则有 $p(n)$的敌手可以在$f(n)$内有效攻破（break）$\pi(n)$

对于命题的证明通常是构造一个具体的多项式$q_p, q_f, s.t. p'\leq q_p and\ f'\leq q_f\cdot f\ (or p' \leq q_p(p), f' \leq q_f(f)$

如果 $f' \approx f\ and\ p' \approx p $，就称为紧归约 tight reduction

例如：如果假设 $\pi$ 是安全的，那么对于 $\Pi$ 的安全性保证需要构造这样一个归约：如果没有敌手能够使用 $2^n$ CPU时间以 $2^{-n}$ 概率攻破 $\pi$，则没有敌手可以使用 $2^n/q_p(n)$ CPU时间以 $2^{-n} \cdot q_f(n)$ 概率攻破 $\Pi$

## 计算安全性加密

Computationally secure encryption

**Definition 3.7** 私钥加密方法是一个元组$(Gen, Enc, Dec)$的PPT算法

1. 密钥生成：$k \leftarrow Gen(1^n)$，由安全参数 n 映射到密钥 k ，输出的 k 是均匀分布的 ${\{0,1\}}^n$，这里假设 $|k| \geq n$
2. 加密：(possible probabilistic， 概率) 加密算法 $Enc$ 将密钥 k 和明文 $m\in {\{0,1\}}^*$ 映射到密文 $c \leftarrow Enc_k(m)$，加密算法是多项式时间的
3. 解密： $m:=Dec_k(c)$是确定的

正确性：对任意正确的安全参数 n 和所有 $m\in {\{0,1\}}^*$，$Pr[Dec_k(Enc_k(m))|k \leftarrow Gen(1^n)]=1$

- stateless无状态：任两次调用独立，定义3.7指的是无状态
- stateful有状态

## 一种完善保密性的计算安全性变体

- 只要求对于有限计算能力的敌手是安全的
- 只要求是小概率不被攻破，而非完美
- 需要加密的消息可以是任意长度
- 加密密钥只能使用一次的要求仍然存在

$PrivK_{A,\Pi}^{eav} security$

![image-20210609155034854](.\image\image-20210609155034854.png)

**Definition 2.5** 加密方案$\Pi=(Gen; Enc; Dec)$是完美不可区分Perfectly indistinguishable，如果对于$\forall A$有 $Pr[PrivK_{A, \Pi}^{eav}=1]=\frac{1}{2}$

**Definition 3.8** 加密方案$\Pi=(Gen; Enc; Dec)$是不可区分的加密如果对于任何PPT的敌手$A$，有$Pr[PrivK_{A, \Pi}^{eav}=1]\leq\frac{1}{2}+negl(n)$

**Definition 3.9** 加密方案$\Pi=(Gen; Enc; Dec)$是不可区分的加密如果对于任何PPT的敌手$A$，有$|Pr[out_A(PrivK_{A, \Pi}^{eav}(n,0)=1)]-Pr[out_A(PrivK_{A, \Pi}^{eav}(n,1)=1)]|\leq negl(n)$

## 明文长度

Plaintext Length

- 完善保密性隐藏了加密消息的所有信息，但暴露了“有一条秘密消息被发送”的信息
- 隐写术Steganograph解决了隐藏秘密消息的问题
- 计算安全性加密揭露了明文长度

一般而言，泄露明文长度没有问题，但长度可能也是敏感信息，所以需要记住计算安全性中没有隐藏明文长度。

## 语义安全

Semantic Security

语义安全隐藏了明文的所有信息。对所有 PPT 敌手，

![image-20210609161209322](.\image\image-20210609161209322.png)

Theorem 3.13 一个私钥加密具有不可区分性当且仅当它是语义安全的。

## 伪随机

Pseudorandomness

如果一个样本分布不能有效地和随机均匀样本区分开，那就是伪随机的。

![image-20210609163106139](.\image\image-20210609163106139.png)

### 伪随机生成器

Pseudorandomness Generator，PRG

![image-20210609163137971](.\image\image-20210609163137971.png)

- 扩展性：生成的伪随机数长度比输入长度大
- 伪随机性

## 使用短密钥加密长消息

![image-20210609164027120](.\image\image-20210609164027120.png)

## 规约性证明

Proofs by reduction：假设存在一个A可以攻破一个方案，则可以构造一个$A'$可以攻破底层模块或者解决依赖的计算困难问题。

![image-20210609164956822](.\image\image-20210609164956822.png)

1. A在一个多项式时间$\epsilon(n)$内攻破问题$\Pi$

2. 构造一个效率算法$A'$，以A为子程序，$A'$并不需要知道A如何实现，只知道A可以攻破$\Pi$。因此，给$A'$一个X的实例x，$A'$会给A提供一个$\Pi$的模拟：

   I> A只能解决$\Pi$，所以$A'$要使得A认为是在解决$\Pi$

   II> 如果A解决了由$A'$构造的$\Pi$，则$A'$也可以通过逆多项式概率至少$1/p(n)$解决X的实例x

3. 由2可得，$A'$可以以$\epsilon(n)/p(n)$解决X，如果$\epsilon(n)$是不可忽略的，则$\epsilon(n)/p(n)$也不是。由此，$A'$可以以不可忽略的可能性解决问题X，与最初的假设相矛盾。

4. 由此就能反证出$\Pi$是计算性安全的。

## Stronger Security Notions

以上是敌手只能得到一个密文的情况下，

CPA chosen-plaintext attack：选择密文攻击，可以得到任意多的密文

- non-adaptive CPA：敌手想要得到的密文一次得到（非适应性）
- adaptiva CPA：可以一直请求（适应性）-> CPA security

### IND-CPA

![image-20210609184940802](.\image\image-20210609184940802.png)

![image-20210609185051422](.\image\image-20210609185051422.png)

### Left-or-Right CPA security

