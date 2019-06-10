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

中文视频地址：https://v.qq.com/x/page/g0827bvdnc2.html

英文视频地址：https://drive.google.com/open?id=1Uk3-n_pttija8J5lcrBCNbUEZkGpZaQQ

## 2. Storage API 使用方法

### 2.1 GetContext & GetReadOnlyContext

GetContext & GetReadOnlyContext获取当前智能合约的运行的上下文环境，一般情况下为当前智能合约hash的反序。如图，返回值是右上角显示的合约哈希的反序。

![1](https://upload-images.jianshu.io/upload_images/150344-8c4ab81273a487f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2.2 Put

Put负责将数据以**字典**形式存入区块链。如图所示，Put接受三个参数。GetContext获取当前智能合约的运行的上下文环境， key - 当前需要存储数据的 key 值，value - 当前需要存储数据的 value 值。

![2](https://upload-images.jianshu.io/upload_images/150344-f4a8dc4e3df59f7e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2.3 Get

Get负责通过key值来读取存在区块链中的数据，在右侧参数面板处填入key值。如图所示：

![3](https://upload-images.jianshu.io/upload_images/150344-da9134fdd6dcd9e5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2.4 Delete

Delete负责通过key值来删除存在区块链中的数据，在右侧参数面板处填入key值。如图所示：

![4](https://upload-images.jianshu.io/upload_images/150344-e6376ea423e56da5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


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

区块链存储是区块链整个体系的核心，本体Storage API的使用方法非常简洁，对开发者非常友好。另一方面，存储是黑客攻击的重点，例如在之前的一期中我们提及的一种安全威胁：存储注入攻击，开发者在写存储相关代码时务必注意代码安全。下一期我们将讨论Runtime API使用方法，敬请期待！

## 5.问题

GetContext的返回值是：
1. 当前合约哈希
2. 当前合约代码
3. 当前合约哈希的反序
4. 当前合约AVM字节码

答案是C
