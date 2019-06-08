<h1 align="center">本体智能合约语法专辑（3）- Blockchain & Block API</h1>
<p align="center" class="version">Version 0.1</p>

### 1.导语

在上两期中，我们介绍了什么是本体智能合约和合约开发工具SmartX。这一期将正式开始智能合约语法部分。 本体的智能合约API分为7个模块。Blockchain & Block API, Runtime API, Storage API, Native API, Upgrade API, Execution Engine API, Static & Dynamic Call API. 本期我们将介绍 Blockchain & Block API。Blockchain API支持基本的区块链查询操作例如获取当前块高，Block API支持基本的区块查询操作例如查询指定区块交易数。下文将一一提供调用方法，所有语法部分提供中英文视频。

中文视频地址：
https://v.qq.com/x/page/x0822d6u8xl.html

英文视频地址：
https://drive.google.com/open?id=11MkWjXEpDM0w83flg0rMvQ4Br6UIsC1q

## 2. Blockchain API使用方法

### 2.1 Import 引用

```
from ontology.interop.System.Blockchain import GetHeight, GetHeader, GetBlock, GetTransactionHeight, GetContract, GetTransactionByHash
```

### 2.2 Blockchain API

#### GetHeight 获取当前最新块高

```
from boa.interop.System.Runtime import Notify
from boa.interop.System.Blockchain import GetHeight

def Main(operation):
if operation == 'demo':
return demo()
return False


def demo():
height=GetHeight()
Notify(height)
return height
```

#### GetHeader 通过块高获取区块头

```
from ontology.interop.System.Runtime import Notify
from ontology.interop.System.Blockchain import GetHeader

def Main(operation, args):
if operation == 'demo':
return demo()
return False


def demo():
block_height=10
header=GetHeader(block_height)
Notify(header)
return header
```



#### GetTransactionByHash 通过交易哈希获取交易

交易哈希以bytearray的格式，作为参数传入GetTransactionByHash。这个函数的关键在于如何转换16进制格式的交易哈希，将其变为bytearray格式的交易哈希。

以16进制格式的交易哈希"9f270aa3a4c13c46891ff0e1a2bdb3ea0525669d414994aadf2606734d0c89c1"为例。首先将其反序得到“c1890c4d730626dfaa9449419d662505eab3bda2e1f01f89463cc1a4a30a279”，你可以通过SmartX提供的转换工具Hex Number(little endian) <--> Number实现这一步。然后将其转成bytearray格式{0xc1,0x89,0x0c,0x4d,0x73,0x06,0x26,0xdf,0xaa,0x94,0x49,0x41,0x9d,0x66,0x25,0x05,0xea,0xb3,0xbd,0xa2,0xe1,0xf0,0x1f,0x89,0x46,0x3c,0xc1,0xa4,0xa3,0x0a,0x27,0x9f}，你可以通过SmartX提供的转换工具String <--> Byte Array实现这一步。最后，将得到的bytearray转换成"\xc1\x89\x0c\x4d\x73\x06\x26\xdf\xaa\x94\x49\x41\x9d\x66\x25\x05\xea\xb3\xbd\xa2\xe1\xf0\x1f\x89\x46\x3c\xc1\xa4\xa3\x0a\x27\x9f"。

```
from ontology.interop.System.Blockchain import GetHeight, GetHeader, GetBlock, GetTransactionHeight, GetContract, GetTransactionByHash

def Main(operation):
if operation == 'demo':
return demo()
return False


def demo():
# tx_hash="9f270aa3a4c13c46891ff0e1a2bdb3ea0525669d414994aadf2606734d0c89c1"
tx_hash=bytearray(b"\xc1\x89\x0c\x4d\x73\x06\x26\xdf\xaa\x94\x49\x41\x9d\x66\x25\x05\xea\xb3\xbd\xa2\xe1\xf0\x1f\x89\x46\x3c\xc1\xa4\xa3\x0a\x27\x9f")
tx=GetTransactionByHash(tx_hash)
return tx
```

#### GetTransactionHeight 通过交易哈希获取交易高度

```
from ontology.interop.System.Blockchain import GetHeight, GetHeader, GetBlock, GetTransactionHeight, GetContract, GetTransactionByHash

def Main(operation):
if operation == 'demo':
return demo()
return False


def demo():
# tx_hash="9f270aa3a4c13c46891ff0e1a2bdb3ea0525669d414994aadf2606734d0c89c1"
tx_hash=bytearray(b"\xc1\x89\x0c\x4d\x73\x06\x26\xdf\xaa\x94\x49\x41\x9d\x66\x25\x05\xea\xb3\xbd\xa2\xe1\xf0\x1f\x89\x46\x3c\xc1\xa4\xa3\x0a\x27\x9f")
height=GetTransactionHeight(tx_hash)
return height
```

#### GetContract 通过合约哈希获取合约

合约哈希的转换过程与交易哈希一致。

```
from ontology.interop.System.Blockchain import GetHeight, GetHeader, GetBlock, GetTransactionHeight, GetContract, GetTransactionByHash

def Main(operation):
if operation == 'demo':
return demo()
return False


def demo():
# contract_hash="d81a75a5ff9b95effa91239ff0bb3232219698fa"
contract_hash=bytearray(b"\xfa\x98\x96\x21\x32\x32\xbb\xf0\x9f\x23\x91\xfa\xef\x95\x9b\xff\xa5\x75\x1a\xd8")
contract=GetContract(contract_hash)
return contract
```

#### GetBlock 

有两种方法获取指定区块。第一种，通过块高获取区块。第二种，通过区块哈希获取区块。

1. Get block by height 通过块高获取区块

```
from ontology.interop.System.Blockchain import GetHeight, GetHeader, GetBlock, GetTransactionHeight, GetContract, GetTransactionByHash

def Main(operation):
if operation == 'demo':
return demo()
return False


def demo():
block=GetBlock(1408)
return block
```

2. Get block by block hash 通过区块哈希获取区块


```
from ontology.interop.System.Blockchain import GetHeight, GetHeader, GetBlock, GetTransactionHeight, GetContract, GetTransactionByHash

def Main(operation):
if operation == 'demo':
return demo()
return False


def demo():
block_hash=bytearray(b'\x16\xe0\xc5\x40\x82\x79\x77\x30\x44\xea\x66\xc8\xc4\x5d\x17\xf7\x17\x73\x92\x33\x6d\x54\xe3\x48\x46\x0b\xc3\x2f\xe2\x15\x03\xe4')
block=GetBlock(block_hash)
```

## 3. Block API 使用方法

### 3.1 Import 引用

```
from ontology.interop.System.Block import GetTransactions, GetTransactionCount, GetTransactionByIndex
```

### 3.2 Block API

#### GetTransactionCount 获取指定区块的交易数量

```
from ontology.interop.System.Blockchain import GetBlock
from ontology.interop.System.Block import GetTransactionCount

def Main(operation):
if operation == 'demo':
return demo()
return False


def demo():
block=GetBlock(1408)
count=GetTransactionCount(block)
return count
```

#### GetTransactions 获取指定区块的所有交易

```
from ontology.interop.System.Blockchain import GetBlock
from ontology.interop.System.Block import GetTransactionCount

def Main(operation):
if operation == 'demo':
return demo()
return False


def demo():
block=GetBlock(1408)
txs=GetTransactions(block)
return txs
```

#### GetTransactionByIndex 获取指定区块的指定交易

```
from ontology.interop.System.Blockchain import GetBlock
from ontology.interop.System.Block import GetTransactionByIndex

def Main(operation):
if operation == 'demo':
return demo()
return False


def demo():
block=GetBlock(1408)
tx=GetTransactionByIndex(block,0) # index starts from 0.
return tx
```


