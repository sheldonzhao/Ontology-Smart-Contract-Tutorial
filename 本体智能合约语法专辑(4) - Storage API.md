<h1 align="center">本体智能合约语法专辑(4) - Storage API</h1>

## 1. 导语

上一期我们正式开始了本体智能合约语法部分，讲述了Blockchain & Block API 的用法。本期我们讨论如何使用Storage API （存储 API）。Storage API 共有5个相关的 API，实现了对区块链的增删改查。具体用法如下：

| API 名称                                     | 返回值            | 描述                              |
| ---------------------------------------- | -------------- | -------------------------------- |
| Storage.GetContext                   | StorageContext | 当前智能合约的运行的上下文环境，一般情况下为当前智能合约 hash               |
| Storage.GetReadOnlyContext | Read-only StorageContext | 同上，但合约只读，即只能调用Get API|
| Storage.Get(StorageContext, key)       | byte[]         | 根据key值读取数据   |
| Storage.Put(StorageContext, key, value) | void       |存储数据，key - 当前需要存储数据的 key 值，value - 当前需要存储数据的 value 值|
| Storage.Delete(StorageContext, key)   | void           | 根据key值删除数据  |

所有语法部分提供中英文视频。

中文视频地址：

英文视频地址：

## 2. Storage API 使用方法



## 3. Storage API 代码示例

```
from ontology.interop.System.Storage import GetContext, Get, Put, Delete, GetReadOnlyContext
from ontology.interop.System.Runtime import Notify

def Main(operation,args):
if operation == 'get_sc':
return get_sc()
if operation == 'get_read_only_sc':
return get_read_only_sc()
if operation == 'get_data':
key=args[0]
return get_data(key)
if operation == 'save_data':
key=args[0]
value=args[1]
return save_data(key, value)
if operation == 'delete_data':
key=args[0]
return delete_data(key)
return False

def get_sc():
return GetContext()

def get_read_only_sc():
return GetReadOnlyContext()

def get_data(key):
sc=GetContext() 
data=Get(sc,key)
return data

def save_data(key, value):
sc=GetContext() 
Put(sc,key,value)

def delete_data(key):
sc=GetContext() 
Delete(sc,key)
```

## 4.后记

区块链存储是区块链整个体系的核心。
