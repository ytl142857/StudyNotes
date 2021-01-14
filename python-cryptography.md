# Python-cryptography

cryptography是一个提供加密算法的python包。致力于成为一个python加密的标准库。

> [github地址](https://github.com/pyca/cryptography)
>
> [pypi地址](https://pypi.org/project/cryptography/)
>
> [官方文档](https://cryptography.io/en/latest/)

## 1. 简介

### 1.1 特性 Layout

cryptography大概分为两个层次。

一是安全的密码学方法层（称之为 The Recipes Layer），它们几乎不需要配置就可以使用。并且它们非常安全，开发者不需要做过多决策。

二是处于底层的密码学原语（称之为 The Hazardous Materials Layer），包括哈希函数，签名等。这些需要对密码学有较深的理解，在工作中使用更是需要慎之又慎。这些功能被放在了`cryptography.hazmat`包中，并且它们的文档中也包含了警告。

建议尽可能直接使用安全的密码学方法，在必要时再使用密码学原理的方法。

### 1.2 安装 installation

- 通过pip安装

```bash
$ pip install cryptography

# 清华镜像
$ pip install -i https://pypi.tuna.tsinghua.edu.cn/simple cryptography
```

- 通过conda安装

```bash
$ conda install cryptography
```

## 2. Fernet 对称加密算法

Fernet保证使用它加密的信息在没有密钥的情况下不能被操纵或读取。Fernet是对称身份验证加密的一种实现。并且，Fernet还支持通过`MultiFernet`实现key rotation。

### 2.1 Fernet使用示例

 ```python
from cryptography.fernet import Fernet

key = Fernet.generate_key()
print(key)
f = Fernet(key)

token = f.encrypt(b"hello world")

print(token)

ans = f.decrypt(token)
print(ans)
 ```

```python
key = b'PlMpWtMVaVCjFRUxgEuhkOf9EL7YNFwsu5ETYvdwRbw='

token = b'gAAAAABf4EZ44y-VVhZvKpOh2_NtLfs9qvW1kliyuJ7Juf2G6yxGQEzsOcP4EuMbMdMpXs5JBB48LV1X4ABJUE-vm0lq0TqRCg=='

ans = b'hello world'
```

由于是对称加密，需要保证key的安全。

`generate_key()`：生成一个fernet密钥。如果你丢失了它，你将不再能够解密信息；如果其他人获得了访问权限，他们将能够解密您的所有消息，他们也将能够伪造将被验证和解密的任意消息。

`encrypt(data)`：加密数据，加密的结果称为“Fernet Token”。注意：加密的数据需要为bytes类型。

`decrypt(token)`：解密数据。

### 2.2 Fernet 实现 key rotation

```python
from cryptography.fernet import Fernet, MultiFernet

key1 = Fernet(Fernet.generate_key())
key2 = Fernet(Fernet.generate_key())

f = MultiFernet([key1, key2])

token = f.encrypt(b"Hello World!")

print(b"token="+token)

ans = f.decrypt(token)

print(b"ans="+ans)
```

MultiFernet使用所提供列表中的第一个密钥执行所有加密选项，MultiFernet尝试依次使用每个密钥解密令牌。如果在提供的列表中没有找到正确的密钥，将引发InvalidToken异常。

### 2.3 实现

Fernet建立于一系列标准的密码学原语上，例如：

- 128位的CBC模式AES加密，通过PKCS7填充。
- HMAC使用SHA256进行身份验证。
- 使用`os.urandom()`生成初始化向量。

完整细节请见[详述](https://github.com/fernet/spec/blob/master/Spec.md)

### 2.4 局限性

Fernet适合于用来加密容易装入内存的数据，由于设计特性，它不会暴露未经身份验证的字节，这意味着完整的消息内容必须存储在内存中，因此Fernet一般不适合非常大的文件加密。

## 3. X.509

## 4. 认证加密 Authenticated Encryption

关联数据的认证加密（Authenticated encryption with associated data （AEAD））是为其密文提供保密性和完整性的加密方案。它们还支持为未加密的关联数据提供完整性。

### 4.1 ChaCha20Poly1305

> ChaCha20Poly1305 结构在 RFC 7539 section 2.8 中定义。它是一个与 MAC 结合的流密码，提供了强大的完整性保证。

```python
import os
from cryptography.hazmat.primitives.ciphers.aead import ChaCha20Poly1305

data = b'Hello World!'
aad = b'authenticated but unencrypted data'

key = ChaCha20Poly1305.generate_key()
chacha = ChaCha20Poly1305(key)

nonce = os.urandom(12)

ct = chacha.encrypt(nonce, data, aad)

ans = chacha.decrypt(nonce, ct, aad)

print(ans)
```

`generate_key()`：生成一个32位的 ChaCha20Poly1305 密钥。

`encrypt(nonce, data, associated_data)`：加密提供的数据并认证associated_data。

`nonce`：一个12位的bytes类型值。注意：不要重复使用nonce

`decrypt(nonce, data, associated_data)`：解密数据。

### 4.2 AES-GCM

AES-GCM结构由 Galois Counter Mode (GCM)的AES分组密码组成。

```python
import os
from cryptography.hazmat.primitives.ciphers.aead import AESGCM

data = b'Hello World!'
aad = b'authenticated but unencrypted data'

key = AESGCM.generate_key(bit_length=128)
aesgcm = AESGCM(key)

nonce = os.urandom(12)

ct = aesgcm.encrypt(nonce, data, aad)

ans = aesgcm.decrypt(nonce, ct, aad)

print(ans)
```

`generate_key(bit_length)`：生成一个 AESGCM 密钥，长度只能为128，192或者256。

### 4.3 AES-CCM

AES-CCM结构由AES分组密码组成，使用的计数器是CBC-MAC。

```python
import os
from cryptography.hazmat.primitives.ciphers.aead import AESCCM

data = b'Hello World!'
aad = b'authenticated but unencrypted data'

key = AESCCM.generate_key(bit_length=128)
aesccm = AESCCM(key)

nonce = os.urandom(12)

ct = aesccm.encrypt(nonce, data, aad)

ans = aesccm.decrypt(nonce, ct, aad)

print(ans)
```

## 5. 非对称加密 Asymmetric Algorithms

非对称密码学是密码学的一个分支，其中一个秘密密钥可以分为两个部分，一个公钥和一个私钥。公钥可以授予任何人，不管是否可信，而私钥必须保密。

非对称加密有两个主要用途：身份验证和机密性。使用非对称加密，可以使用私钥对消息进行签名，然后拥有公钥的任何人都能够验证消息是由拥有相应私钥的人创建的。这可以与身份证明系统结合使用，以了解实际拥有该私钥的实体（个人或组），并提供身份验证。拥有公钥的人能够加密消息，提供机密性，然后只有拥有私钥的人才能够解密消息。

### 5.1 Ed25519 Signing

Ed25519是利用EdDSA和Curve25519实现的椭圆曲线签名算法。

- 签名&验证

```python
from cryptography.hazmat.primitives.asymmetric.ed25519 import Ed25519PrivateKey

private_key = Ed25519PrivateKey.generate()
signature = private_key.sign(b'Hello World!')

public_key = private_key.public_key()

# 如果验证不通过，会抛出 InvalidSignature 异常
public_key.verify(signature, b'Hello Worl')
```



### 5.2 X25519 Key Exchange

### 5.3 Ed448 Signing

### 5.4 X448 Key Exchange

### 5.5 Elliptic Curve Cryptography（ECC椭圆曲线算法）

ECDSA签名算法首先在NIST公布的FIPS 186-3中标准化，后来在FIPS 186-4中标准化。

虽然椭圆曲线密钥既可用于签名也可用于密钥交换，但这并不好。相反，用户应该生成单独的签名和ECDH密钥。

- Elliptic Curve Signature Algorithms 签名算法

- Elliptic Curve Key Exchange algorithm 密钥交换算法



### 5.6 RSA

RSA是一种用于加密和签名的公钥算法。

与对称密钥不同，对称密钥中的密钥通常只是一个随机的字节序列，RSA密钥是具有特定数学属性的复杂内部结构。

`cryptography.hazmat.primitives.asymmetric.rsa.generate_private_key(public_exponent, key_size, backend=None)`：使用后生成一个新的RSA私钥。

- `key_size`：密钥长度，2048位或4096位是较为安全的，1024位及以下是不安全的。
- `public_exponent`：生成键的一个数学属性，一般为65537

```python
from cryptography.hazmat.primitives.asymmetric import rsa

private_key = rsa.generate_private_key(
    public_exponent=65537,
    key_size=2048,
)
```

**加载密钥**：如果你在磁盘中已经存在一个PEM的密钥，你可以加载它

```python
from cryptography.hazmat.primitives import serialization

with open("path/to/key.pem", "rb") as key_file:
    private_key = serialization.load_pem_private_key(
        key_file.read(),
        password=None,
    )
```

可以选择使用密码在磁盘上加密序列化密钥。在本例中，我们加载了一个未加密的密钥，因此我们没有提供密码。如果密钥被加密，我们可以传递一个bytes对象作为密码参数。

**序列化密钥**：如果已经加载或生成了一个实现了RSAPrivateKeyWithSerialization接口的私钥，您可以使用private_bytes()来序列化该私钥。

**签名**：私钥可用于对消息进行签名。这允许拥有公钥的任何人验证消息是由拥有相应私钥的人创建的。RSA签名需要使用特定的散列函数和填充。下面是一个使用RSA签名消息的例子，带有安全哈希函数和填充:

```python
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.primitives.asymmetric import padding

message = b'Hello World'
signature = private_key.sign(
    message,
    padding.PSS(
        mgf=padding.MGF1(hashes.SHA256()),
        salt_length=padding.PSS.MAX_LENGTH
    ),
    hashes.SHA256()
)
```

**验证**：

```python
public_key = private_key.public_key()
public_key.verify(
    signature,
    message,
    padding.PSS(
        mgf=padding.MGF1(hashes.SHA256()),
        salt_length=padding.PSS.MAX_LENGTH
    ),
    hashes.SHA256()
)
```

**加密&解密**：RSA加密由于其非对称性，任何人都可以使用公钥加密数据，然后使用私钥解密数据。

```python
message = b'Hello World'
ciphertext = public_key.encrypt(
    message,
    padding.OAEP(
        mgf=padding.MGF1(algorithm=hashes.SHA256()),
        algorithm=hashes.SHA256(),
        label=None
    )
)

plaintext = private_key.decrypt(
    ciphertext,
    padding.OAEP(
        mgf=padding.MGF1(algorithm=hashes.SHA256()),
        algorithm=hashes.SHA256(),
        label=None
    )
)

print(plaintext)
```

### 5.7 Diffie-Hellman Key Exchange DH密钥交换

DH密钥交换循序双方在不安全的信道中通信

### 5.8 DSA

### 5.9 密钥序列化

序列化与反序列化的方法

## 6. 结语

本文是对python crytography库的文档进行了阅读和部分翻译，更多详细内容还请转到[官方文档](https://cryptography.io/en/latest/)