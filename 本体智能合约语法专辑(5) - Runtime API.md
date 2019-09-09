<h1 align="center">本体智能合约语法专辑(5) - Runtime API</h1>

## 1. 导语

上一期我们介绍了本体智能合约存储API，本期我们讨论如何使用Runtime API （合约执行 API）。Runtime API 共有8个相关的 API，提供了合约执行时常用的接口，帮助开发者获取数据，转换数据，验证数据。具体用法如下：

| API 名称                                     | 返回值            | 描述                              |
| ---------------------------------------- | -------------- | -------------------------------- |
| GetTime                 | uint | 返回当前时间戳          |
| GetCurrentBlockHash|string | 返回当前区块的区块哈希 |
| Notify(object[])     | void | 推送事件到全网 |
| Serialize(item) |byte array |将一个对象序列化成bytearray对象|
| Deserialize(item)|original type | 将 bytearray反序列化成原先对象|
| Base58ToAddress(base58_address)|byte array|将 base58 地址转成 bytearray形式地址|
| AddressToBase58(address)|string |将 bytearray形式地址转成base58 地址 |
| CheckWitness(fromAcct) | bool | 验签  |

下面我们具体讲述一下这五个 API 的使用方法。在这之前，小伙伴们可以在本体智能合约开发工具 SmartX 中新建一个合约，跟着我们进行操作。同样，在文章最后我们将给出这次讲解的所有源代码以及视频讲解。
所有语法部分提供中英文视频。中文视频地址：[https://v.qq.com/x/page/p08274qq2jw.html](https://v.qq.com/x/page/p08274qq2jw.html)


## 2. Runtime API 使用方法

### 引用 

Runtime API的引用分为两个路径，分别是ontology.interop.System.Runtime和ontology.interop.Ontology.Runtime。Ontology路径下包含了新增的API。

```
from ontology.interop.System.Runtime import GetTime, CheckWitness, Notify, Serialize, Deserialize
from ontology.interop.Ontology.Runtime import Base58ToAddress, AddressToBase58, GetCurrentBlockHash
```

### Notify API

`Notify`: 推送事件到全网

```
from ontology.interop.System.Runtime import Notify

def demo():
    Notify("hello world") 
```

Notify将会返回"hello world"十六进制字符串，并将其推送全网

![](https://upload-images.jianshu.io/upload_images/150344-06efcf5a9e3c9610.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### GetTime API
`GetTime`: 返回当前时间戳

```
from ontology.interop.System.Runtime import GetTime

def demo():
    time=GetTime()
    return time # 返回时间戳, 单位是秒
```

### GetCurrentBlockHash API

`GetCurrentBlockHash`: 返回当前区块的区块哈希

```
from ontology.interop.Ontology.Runtime import GetCurrentBlockHash
    
def demo():
    block_hash = GetCurrentBlockHash()
    return block_hash
```

### Serialize and Deserialize

`Serialize`: 将一个对象序列化成bytearray对象

`Deserialize`: 将 bytearray反序列化成原先对象

```
from ontology.interop.System.Runtime import Notify, Serialize, Deserialize
from ontology.interop.System.Storage import Put, Get, GetContext

def Main(operation, args):
    if operation == 'serialize_to_bytearray':
        data = args[0]
        return serialize_to_bytearray(data)
    if operation == 'deserialize_from_bytearray':
        key = args[0]
        return deserialize_from_bytearray(key)
    return False

def serialize_to_bytearray(data):
    sc = GetContext()
    key = "1"
    byte_data = Serialize(data) # 序列化传入的参数
    Put(sc, key, byte_data) # 将序列化后的数据存入区块链


def deserialize_from_bytearray(key):
    sc = GetContext()
    byte_data = Get(sc, key) # 按key从区块链中取出数据
    data = Deserialize(byte_data) 将bytearray数据反序列化成原先类型数据
    return data
```

### Base58ToAddress and AddressToBase58
`Base58ToAddress`: 将 base58 地址转成 bytearray形式地址

`AddressToBase58`: 将 bytearray形式地址转成base58 地址

```
from ontology.interop.Ontology.Runtime import Base58ToAddress, AddressToBase58

def demo():
    base58_addr="AV1GLfVzw28vtK3d1kVGxv5xuWU59P6Sgn"
    addr=Base58ToAddress(base58_addr) # 将 base58 地址转成 bytearray形式地址
    Notify(addr)
    base58_addr=AddressToBase58(addr) #将bytearray数据反序列化成原先类型数据
    Notify(base58_addr)
```

### CheckWitness

**CheckWitness(fromAcct)** 有两个功能：

1.  验证当前的函数调用者是不是 `fromAcct` ,若是（验证签名），则验证通过。

2.  检查当前函数调用者是不是一个合约 A，若是合约 A，且是从合约 A 发起的去执行函数，则验证通过(验证 `fromAcct` 是不是[`GetCallingScriptHash()` ](https://github.com/ontio/ontology-python-compiler/blob/master/ontology/interop/System/ExecutionEngine.py)的返回值)。

```
from ontology.interop.System.Runtime import CheckWitness
from ontology.interop.Ontology.Runtime import Base58ToAddress

def demo():
    addr=Base58ToAddress("AW8hN1KhHE3fLDoPAwrhtjD1P7vfad3v8z")
    res=CheckWitness(addr) # 验证调用者地址是否为AW8hN1KhHE3fLDoPAwrhtjD1P7vfad3v8z
    return res
```


## 5. 本期问答

哪些类型数据最好需要serialize API 转换后才能存入区块链（多选题）
A. List
B. Map
C. String
D. Integer
E. Byte array



