<h1 align="center">本体智能合约语法专辑(10) - ExecutionEngine API</h1>

## 1. 导语

上二期我们介绍了跨合约静态调用与动态调用，讲述了如何使用RegisterAppCall 与  DynamicAppCall API 跨合约调用其他合约的函数。本期我们讨论如何使用 ExecutionEngine API （合约执行引擎API）。它包含了3个API，用法如下：

| API                          |返回值  |描述                                       |
| ---------------------------- | ---- | ---------------------------------------- |
| GetExecutingScriptHash | bytearray |  返回当前合约的合约哈希反序|
|GetCallingScriptHash | bytearray | 上一级调用者的脚本哈希，与合约，函数相关 |
|GetEntryScriptHash | bytearray | 入口调用者的脚本哈希，与最初调用者相关 |

看的云里雾里对不对？本期语法难度较大，下面我们具体讲述一下 ExecutionEngine API 的使用方法。在这之前，小伙伴们可以在本体智能合约开发工具 SmartX 中新建一个合约，跟着我们进行操作。同样，在文章最后我们将给出这次讲解的所有源代码以及视频讲解。
所有语法部分提供中英文视频。中文视频地址：[https://v.qq.com/x/page/s0831u6a9xw.html](https://v.qq.com/x/page/s0831u6a9xw.html)

## 2. ExecutionEngine API 使用方法

### 引用

```
from ontology.interop.System.ExecutionEngine import GetExecutingScriptHash, GetCallingScriptHash, GetEntryScriptHash
```

### GetExecutingScriptHash 

GetExecutingScriptHash API最为简单，它的作用是返回当前合约的合约哈希反序。

```
from ontology.interop.System.ExecutionEngine import GetExecutingScriptHash

def Main(operation, args):
    if operation == "get_contract_hash":
        return get_contract_hash()
    
    return False

def get_contract_hash():
    return GetExecutingScriptHash()
```

如图，右上角Basic Info显示了合约哈希。左下角控制台返回了当前合约哈希的反序。

![Screen Shot 2019-09-26 at 11.52.32 AM.png](https://upload-images.jianshu.io/upload_images/150344-52f30268c2139474.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### GetCallingScriptHash

GetCallingScriptHash API返回上一级调用者的脚本哈希，与合约，函数相关。因此不同合约，不同函数调用 GetCallingScriptHash都会得到不同的脚本哈希，因为他们（合约，函数）是不同的调用者。

```
from ontology.interop.System.ExecutionEngine import GetExecutingScriptHash, GetCallingScriptHash

def Main(operation, args):
    if operation == "GetCallingScriptHash_test1":
        return GetCallingScriptHash_test1()
    if operation == "GetCallingScriptHash_test2":
        return GetCallingScriptHash_test2()
    
    return False


def GetCallingScriptHash_test1():
    return GetCallingScriptHash()
    
    
def GetCallingScriptHash_test2():
    return GetCallingScriptHash()
```

如图所示，GetCallingScriptHash_test1函数与GetCallingScriptHash_test2函数返回了两个不同的脚本哈希。**将相同的函数放入不同的合约，也会返回不同的脚本哈希**。

![Screen Shot 2019-09-26 at 2.38.12 PM.png](https://upload-images.jianshu.io/upload_images/150344-0e986ae5ede89024.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### GetEntryScriptHash

在智能合约的调用中，有直接调用者就有间接调用者（跨合约调用）。GetEntryScriptHash，它会返回入口（最初）调用者的脚本哈希 。

合约B

```
from ontology.interop.System.ExecutionEngine import GetExecutingScriptHash, GetCallingScriptHash, GetEntryScriptHash
from ontology.interop.System.Runtime import CheckWitness, GetTime, Notify, Serialize, Deserialize

def Main(operation, args):
    if operation == "invokeB":
        return invokeB()
    if operation == "avoidToBeInvokedByContract":
        return avoidToBeInvokedByContract()
        
    return False


def invokeB():
    # the result of GetCallingScriptHash and GetEntryScriptHash is same
    # if they are invoked by the same contract
    callerHash = GetCallingScriptHash()
    entryHash = GetEntryScriptHash()
    Notify([callerHash, entryHash])
    return [callerHash, entryHash]

def avoidToBeInvokedByContract():
    # the purpose is to prevent hack from other contract
    callerHash = GetCallingScriptHash()
    entryHash = GetEntryScriptHash()
    if callerHash != entryHash:
        Notify(["You are not allowed to invoke this method through contract"])
        return False
    else:
        Notify(["You can implement what you need to do here!"])
        return True
```

合约A，调用合约B。

```
from ontology.interop.System.App import RegisterAppCall
from ontology.interop.System.ExecutionEngine import GetExecutingScriptHash, GetCallingScriptHash, GetEntryScriptHash
from ontology.interop.System.Runtime import CheckWitness, GetTime, Notify, Serialize, Deserialize

ContractB = RegisterAppCall('0f44f18d37515a917364362ec256c2110a7b1377', 'operation', 'args')

def Main(operation, args):
    if operation == "invokeA":
        opt = args[0]
        return invokeA(opt)
    if operation == "checkHash":
        return checkHash()
    return False


def invokeA(opt):
    callerHash = GetCallingScriptHash()
    entryHash = GetEntryScriptHash()
    Notify(["111_invokeA",callerHash, entryHash])
    return ContractB(opt, [])


def checkHash():
    Notify(["111_checkHash"])
    callerHash = GetCallingScriptHash()
    entryHash = GetEntryScriptHash()
    Notify([callerHash, entryHash])
    return True
```

如图，首先运行checkHash函数，我们发现在同一个合约中，同一个函数中，调用GetCallingScriptHash与GetEntryScriptHash API返回值相同，都是`a37ca1f1a3421d36b504769a96c06024a07b2bfa `。这是因为他们既是直接调用者，也是最初调用者（没有跨合约调用），所以两个API返回值相同。但如果跨合约调用呢？

![Screen Shot 2019-09-26 at 3.13.36 PM.png](https://upload-images.jianshu.io/upload_images/150344-05eff25b32f6937f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

运行合约A中的invokeA函数。首先还是在同一个合约中，同一个函数中，调用GetCallingScriptHash与GetEntryScriptHash API。Notify返回了两个相同的脚本哈希`11540b9836be257a66c7779fec76fd2e8154b706 `。接着我们看return的值，`"16fda714afa56165fa9e5c5a6dc18347a17c9e02","11540b9836be257a66c7779fec76fd2e8154b706"`, **发现返回值不再相同**。因为在合约B中，GetCallingScriptHash的上一级调用者是合约A与invokeB函数，而GetEntryScriptHash的最初调用者是来自合约A的invokeA函数，因此返回值不同。可以看到返回的`11540b9836be257a66c7779fec76fd2e8154b706``与Notify返回的值相同。

![Screen Shot 2019-09-26 at 3.18.21 PM.png](https://upload-images.jianshu.io/upload_images/150344-f8542e74d451bc61.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 3. 总结

ExecutionEngine API在防范跨合约调用中有广泛的应用场景，因为不是所有合约都允许被跨合约访问（如果有安全漏洞，非常容易被攻击）。合约B中的avoidToBeInvokedByContract函数便提供了一个防止跨合约调用的范例，当GetCallingScriptHash()与GetEntryScriptHash()的返回值不相等时，直接return false结束程序。
