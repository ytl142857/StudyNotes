# python与Go之间的tcp通信-传输json消息

1. python作为服务端, tcp作为客户端
2. 传输多条json格式的信息

## python 端 ( 服务端 )

```python
import socket
import json

if __name__ == '__main__':

    IP = "localhost"
    port = 40005
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.bind((IP, port))
    s.listen(1)
    print('listen at port :', port)
    conn, addr = s.accept()
    print('connected by', addr)

    while True:
        data = conn.recv(1024)
        data = data.decode()
        if not data:
            break
        data = json.loads(data)
        print(data['OrderID'])
        # 发送一个消息防止两条json消息粘合
        conn.send(str.encode('1'))
    conn.close()
    s.close()
```

## Go ( 客户端 )

```Go
package main

import (
    "encoding/json"
    "log"
    "net"
)

type Work struct {
    OrderID   string
    OrderName string
    Owner     string
    Sender    string
    Reciever  string
}

func main() {
    var work Work
    var work_list []Work
    work.OrderID = "123"
    work.OrderName = "asdfasf"
    work.Owner = "hi"
    work.Sender = "hi"
    work.Reciever = "hi"
    work_list = append(work_list, work)
    work_list = append(work_list, work)
    work_list = append(work_list, work)
    work_list = append(work_list, work)

    server := "localhost:40005"
    tcpAddr, err := net.ResolveTCPAddr("tcp4", server)
    if err != nil {
        log.Println("Fatal error")
    }
    conn, err := net.DialTCP("tcp", nil, tcpAddr)
    if err != nil {
        log.Println("Fatal error")
    }

    log.Println("connection success")

    for _, value := range work_list {
        recall, err := json.Marshal(value)
        if err != nil {
            log.Println("error")
        }
        conn.Write([]byte(recall))
        buffer := make([]byte, 2048)
        n, err := conn.Read(buffer)
        log.Println(n)
        //conn.Write([]byte(""))
    }

    log.Println("send over")
    conn.Close()
}

```
