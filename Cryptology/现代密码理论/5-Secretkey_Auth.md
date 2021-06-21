## 保密性与完整性

Secrecy vs. Integrity

基本的加密目标：使得多方可以在开放环境中进行安全的交流。

- 保密性（机密性）Secrecy：使得信道中的敌手无法获得消息的任何信息
- 消息认证（消息完整性）：每一方都可以验证收到的消息是来源可靠且未被更改的。

加密 vs. 消息认证：是两个不同的概念

考虑在完善保密性中的一次一密，敌手可以直接对密文进行修改，根本不需要知道明文

同样的攻击方式在各种加密模式中都有效果

## 消息认证码

Message Authentication Codes，MAC

![image-20210620162651564](C:\Users\admin\Desktop\StudyNotes\Cryptology\现代密码理论\image\image-20210620162651564.png)

正确性

规范的验证：典型的MAC是确定性的deterministic，

$Vrfy_k(m,t)=1 <=> Mac_k(m) = t$

对于敌手来说，接收M，T，很难构造一个新的 M',T'使得 $Vrfy_k(M',T')=1$

![image-20210620163134276](C:\Users\admin\Desktop\StudyNotes\Cryptology\现代密码理论\image\image-20210620163134276.png)

- 存在不可伪造性 Existentially unforgeable：A如果能找出任何tag就胜利
- 选择消息Chosen-message，A可以得到任何选择消息的tag
- 适应性Adaptiva：A可以适应性地选择消息

MAC只保证消息是由知道key的人发出的，敌手可以进行重放、打乱顺序、或者删除消息（可以通过序列号或者时间戳来防止）

## Strong MAC

攻破MAC需要找到一个新的（m\*，t\*）组，如果只是找到Vrfy_k(m\*,t)=1是不算的，strong MAC就是在其中攻击者也很难构建一个新的tag

![image-20210620164635786](C:\Users\admin\Desktop\StudyNotes\Cryptology\现代密码理论\image\image-20210620164635786.png)

让攻击者有获取验证结果的能力

![image-20210620173706061](C:\Users\admin\Desktop\StudyNotes\Cryptology\现代密码理论\image\image-20210620173706061.png)

## 通过时间来攻击

攻击者截取消息后，发送给接收者，可以将接收者当做一个验证预言机

不仅可以得到接收者是接受还是拒绝，还可以知道接收者做出判断的时间

接收者按位来对消息进行验证，攻击者可以通过时间判断哪位正确/错误

比如两个消息及验证码只有最后一位不同，则通过时间判断这位的正确，然后就可以一位一位地推测。

### 构造一个定长的MAC

![image-20210620181156156](C:\Users\admin\Desktop\StudyNotes\Cryptology\现代密码理论\image\image-20210620181156156.png)

证明

![image-20210620181216092](C:\Users\admin\Desktop\StudyNotes\Cryptology\现代密码理论\image\image-20210620181216092.png)

## Domain Extension for MACs

域扩展

为任意长消息提供消息验证码

见PPT

CBC-MAC