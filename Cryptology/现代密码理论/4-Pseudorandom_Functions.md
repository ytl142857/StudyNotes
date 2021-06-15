## 伪随机函数

- 伪随机生成器：由随机种子生成随机数（扩展得到），输出的伪随机数不能有效地efficiently与随机均匀一致字符串区分。
- 伪随机函数：一个加密的keyed，计算有效率efficiently computable的函数，不能有效地和随机函数区分开来。

![image-20210615150705186](C:\Users\admin\Desktop\StudyNotes\Cryptology\现代密码理论\image\image-20210615150705186.png)

![image-20210615150827946](C:\Users\admin\Desktop\StudyNotes\Cryptology\现代密码理论\image\image-20210615150827946.png)

## 伪随机置换

Pseudorandom permutations，是一对一的置换

- $Perm_n \in Func_n$ 表示 ${\{0, 1\}}^n$ 的所有置换，则 $|Perm_n|=2^n ! $
- 使用类似伪随机函数的方式来定义伪随机置换

![image-20210615151339053](C:\Users\admin\Desktop\StudyNotes\Cryptology\现代密码理论\image\image-20210615151339053.png)

**Proposition 3.27** 如果 F 是伪随机置换，且 $l_{in}(n) \geq n$，则 F 也是伪随机函数

## 强伪随机置换

Strong pseudorandom permutations， Strong PRP，分组密码就是基于强伪随机置换

如果一个伪随机置换的逆 inverse quries 也是安全的，他就是强伪随机置换

在实际中，分组密码block-ciphers就被设计为强伪随机置换，但不是渐进的安全，而是定长的密钥和分组大小，并且假定暴力法是最好的攻击方式。

DES，分组大小 64，密钥长度56

AES，分组大小128，密钥长度128， 192， 256

## 通过 PRFs/PRPs 构造CPA安全的加密

对于一个强伪随机 $F:{\{0,1\}}^n \cdot {\{0,1\}}^n \rightarrow {\{0,1\}}^n$，可以构造

$Enc(m) = F_k(m), Dec_k(c) = F_k^{-1}(c)$

直接进行 Fk(m)未泄露 m的任何信息，但是不是CPA安全的，因为加密模式是确定的，

由m=m'可以推出 Fk(m)=Fk(m')

正确的构造：

![image-20210615161514103](C:\Users\admin\Desktop\StudyNotes\Cryptology\现代密码理论\image\image-20210615161514103.png)

3.31的证明：（假设其不安全，即敌手可以区分，则可以构造一个D，使得敌手可以分辨随机和伪随机）

***此处看笔记**

- 构造新的加密方法，用随机函数f来替换Fk
- 如果F是PRF，则没有敌手可以以不可忽略的概率识别新构造的方法
- 新构造的方法是CPA安全的，敌手不可分辨

3.30的缺陷在于加密只能用于定长的消息加密，即使可以扩充为任意长消息加密，那么其密钥长度也是比消息长度长很多的

## PRG vs. Stream Cipher & PRP vs. Block cipher

- 现实中，PRG实例化为流密码加密
- PRP实例化为分组加密，实际上分组加密就是PRP

***分组加密不是加密方法 Block cipher is not an encryption scheme**

### 流密码

Stream Ciphers

伪随机生成器 PRG pseudorandom generator

![image-20210615165413003](C:\Users\admin\Desktop\StudyNotes\Cryptology\现代密码理论\image\image-20210615165413003.png)

PSG是不灵活的，要求输出的定长

流密码作为PSG的一个实例，更加灵活，输出的比特是按需逐位产生的

一般而言，流密码是一对确定的算法（init, Next）

- init 初始化，输入是种子s，和可选的初始化向量 initialization vector IV，输出是初始化状态 initial state  st
- Next，输入是 st 输出是一个 比特y和更新状态 update state st'

给出一个流密码加密（Init， Next），可以得到密码函数 keyed function $F^l$

$F_s^l(IV)=GetBits_1(Init(s, IV), 1^l)$

流密码加密是安全的，只要$F^l$是伪随机函数对任意多项式l

#### 流密码操作模式 modes of operation

同步：中间丢失，后面全灭（通常用于参与方之间的单个会话，状态可接受而且消息按顺序，不会丢失）

异步：每部分不同初始向量

![image-20210615171430821](C:\Users\admin\Desktop\StudyNotes\Cryptology\现代密码理论\image\image-20210615171430821.png)

### 分组密码

- construction 3.30只对定长消息有用，消息可以分组进行加密，但密钥是消息的两倍长
- 分组模式提供了为任意长消息进行加密，并且更低的消耗
- 操作模式（mode of operation）指的是将基础的模块组合为有用的加密方法

#### ECB Electronic code book

 电码本模式，*不要使用

是确定的，所以并非CPA安全

![image-20210615172116235](C:\Users\admin\Desktop\StudyNotes\Cryptology\现代密码理论\image\image-20210615172116235.png)

#### CBC Cipher block chaining

![image-20210615172655980](C:\Users\admin\Desktop\StudyNotes\Cryptology\现代密码理论\image\image-20210615172655980.png)

缺点在于加密必须按顺序执行（不能并行，慢）

![image-20210615172710262](C:\Users\admin\Desktop\StudyNotes\Cryptology\现代密码理论\image\image-20210615172710262.png)

不要随意更改密码方案，比如Chained CBC，是不安全的

#### OFB Output feedback

![image-20210615173217839](C:\Users\admin\Desktop\StudyNotes\Cryptology\现代密码理论\image\image-20210615173217839.png)

和CBC比较：

1. Fk不需要是可逆的，可以用PRF代替PRP
2. 明文长度不需要是n的倍数，可以是任意长
3. 和CBC不同，状态变量是安全的（上一个yl在下一个加密中作为IV）
4. 仍然和CBC一样按顺序执行，不过yi可以在mi之前计算，更快

#### CTR Counter

![image-20210615173249569](C:\Users\admin\Desktop\StudyNotes\Cryptology\现代密码理论\image\image-20210615173249569.png)

**Theorem 3.32 如果F是PRF，则CTR是CPA安全的**

