# FISCO-Flask开发

- 使用 python flask框架开发服务端
- 连接FISCO BCOS节点

## 1. 区块链平台搭建

### 1.1 安装FISCO BCOS

>操作系统：Ubuntu 16.04
>
>[FISCO BCOS官方文档](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/installation.html)

安装openssl, curl

`sudo apt install -y openssl curl`

创建目录，下载安装脚本

```bash
## 创建操作目录并进入
cd ~ && mkdir -p fisco && cd fisco

## 下载脚本
curl -#LO https://github.com/FISCO-BCOS/FISCO-BCOS/releases/download/v2.7.0/build_chain.sh && chmod u+x build_chain.sh

## 从gitee下载脚本（然而后续操作并不是从gitee拉取）
curl -#LO https://gitee.com/FISCO-BCOS/FISCO-BCOS/raw/master/tools/build_chain.sh && chmod u+x build_chain.sh
```

搭建单机4节点链

```bash
bash build_chain.sh -l 127.0.0.1:4 -p 30300,20200,8545

## 可以通过指定二进制文件的形式创建
## 可以在https://gitee.com/FISCO-BCOS/FISCO-BCOS/releases/v2.6.0下载文件
bash build_chain.sh -l 127.0.0.1:4 -p 30300,20200,8545 -e bin/fisco-bcos
```

- `-p` 指定端口，分别是p2p_port,channel_port,jsonrpc_port

启动FISCO BCOS链

```bash
bash nodes/127.0.0.1/start_all.sh
```

检查进程是否启动

```bash
ps -ef | grep -v grep | grep fisco-bcos
```

检查日志

```bash
tail -f nodes/127.0.0.1/node0/log/log*  | grep connected
```

检查是否存在共识

```bash
tail -f nodes/127.0.0.1/node0/log/log*  | grep +++
```

### 1.2 配置 python SDK

> 操作系统：Windows
>
> [python SDK安装](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/sdk/python_sdk/install.html)

安装依赖

- python 3.7及以上版本
- git
- Visual C++14库（[这里]( https://pan.baidu.com/s/1ZmDUGZjZNgFJ8D14zBu9og 提取码: zrby)下载）
- 下载Windows版本[solc](https://github.com/ethereum/solidity/releases/download/v0.4.25/solidity-windows.zip)

拉取sdk文件

```git
git clone https://github.com/FISCO-BCOS/python-sdk
```

配置solc编译器

```bash
# 修改client_config.py.template: 
# 配置solc编译器路径，若solc存放路径为D:\\open-source\\python-sdk\\bin\\solc.exe，则solc_path配置如下：
solc_path = "D:\\open-source\\python-sdk\\bin\\solc.exe"

# 将client_config.py.template拷贝到client_config.p
```

安装SDK所需python包依赖（清华源）

```bash
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple -r requirements.txt
```

配置Channel通信（python SDK支持Channel和RPC两种方式和节点进行通信）

1. 在节点目录下config.ini获取`channel_listen_port`
2. 修改 SDK 中的 client_config.py 文件中`channel_host`为实际的IP，`channel_port`为上步获取的`channel_listen_port`
3. 配置证书，将节点sdk目录下所有相关文件拷贝到 SDK 中的 bin 目录
4. 配置证书路径，修改`client_config.py` 的 `channel_node_cert` 和 `channel_node_key`路径

现在就可以通过SDK访问节点了。

### 1.3 部署&调用合约

通过python SDK命令行工具调用

首先编写智能合约，将其置于contracts目录下

HelloWorld.sol

```solidity
pragma solidity ^0.4.24;

contract HelloWorld{
    string name;
    event onset(string newname);
    constructor() public{
       name = "Hello, World!";
    }

    function get() constant public returns(string){
        return name;
    }

    function set(string n) public{
	emit onset(n);
    	name = n;
    }
}
```

在命令行中部署合约

```bash
$ console.py deploy HelloWorld save 

backup [contracts/HelloWorld.abi] to [contracts/HelloWorld.abi.20190807102912]
backup [contracts/HelloWorld.bin] to [contracts/HelloWorld.bin.20190807102912]
INFO >> compile with solc compiler
deploy result  for [HelloWorld] is:
 {
    "blockHash": "0x3912605dde5f7358fee40a85a8b97ba6493848eae7766a8c317beecafb2e279d",
    "blockNumber": "0x1",
    "contractAddress": "0x2d1c577e41809453c50e7e5c3f57d06f3cdd90ce",
    "from": "0x95198b93705e394a916579e048c8a32ddfb900f7",
    "gasUsed": "0x44ab3",
    "input": "0x6080604052......c6f2c20576f726c642100000000000000000000000000",
    "logs": [],
    "logsBloom": "0x000......0000",
    "output": "0x",
    "status": "0x0",
    "to": "0x0000000000000000000000000000000000000000",
    "transactionHash": "0xb291e9ca38b53c897340256b851764fa68a86f2a53cb14b2ecdcc332e850bb91",
    "transactionIndex": "0x0"
}
on block : 1,address: 0x2d1c577e41809453c50e7e5c3f57d06f3cdd90ce 
address save to file:  bin/contract.ini
```

调用合约

```
# 合约地址：0x2d1c577e41809453c50e7e5c3f57d06f3cdd90ce
# 调用接口：get
$./console.py  call HelloWorld 0x2d1c577e41809453c50e7e5c3f57d06f3cdd90ce get 

INFO >> user input : ['call', 'HelloWorld', '0x2d1c577e41809453c50e7e5c3f57d06f3cdd90ce', 'get']

INFO >> call HelloWorld , address: 0x2d1c577e41809453c50e7e5c3f57d06f3cdd90ce, func: get, args:[]
INFO >> call result: ('Hello, World!',)
```

## 2. Flask 服务器开发

### 2.1 构建第一个应用

首先安装 python Flask 包

```bash
pip install Flask
```

在SDK目录下新建`app.py`和templates目录

app.py

```python
from client.common import transaction_common
from flask import Flask, request, render_template


app = Flask(__name__)

# 连接节点
tx_client = transaction_common.TransactionCommon("0x1f494c56c3ad1e6738f3500d19499cd3541160ea", "contracts", "HelloWorld")

@app.route('/index/', methods=['GET','POST'])
def index():
    if request.method=='POST':
        new_str = request.form['new_str']
        # 如果传入了新值，调用合约的set方法
        tx_client.send_transaction_getReceipt("set", (new_str,))
    # 调用合约的get方法，将str值传给模板页面
    get_str = tx_client.call_and_decode("get")
    return render_template("index.html", get_str=get_str)


if __name__ == '__main__':
    app.debug = True
    app.run()
```

templates/index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <form action="" method="post">
        <input type="text" name="new_str">
        <input type="submit" value="提交helloworld字段">
    </form>
    {% if get_str %}
    {{ get_str }}
    {% endif %}
</body>
</html>
```

在命令行中输入`python app.py`即可运行

## 3. 智能合约编写

