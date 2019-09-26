<h1 align="center">本体智能合约语法专辑(9) - 跨合约动态调用</h1>

## 1. 导语

上一期我们介绍了跨合约静态调用，讲述了如何使用RegisterAppCall API跨合约调用其他合约的函数。本期我们讨论如何跨合约静态调用。API只有一个，用法如下：

| API                          |返回值  |描述                                       |
| ---------------------------- | ---- | ---------------------------------------- |
| DynamicAppCall | 调用函数返回的结果 |  跨合约动态调用|

下面我们具体讲述一下DynamicAppCall API 的使用方法。在这之前，小伙伴们可以在本体智能合约开发工具 SmartX 中新建一个合约，跟着我们进行操作。同样，在文章最后我们将给出这次讲解的所有源代码以及视频讲解。
所有语法部分提供中英文视频。中文视频地址：http://v.qq.com/x/page/g0832e8dxmi.html

## 2. DynamicAppCall 使用方法

### 引用

```
from ontology.interop.System.App import DynamicAppCall
```

### DynamicAppCall API

DynamicAppCall API 有三个参数。分别对应目标合约，目标方法，方法需要的参数。我们准备两个智能合约，合约A与合约B。接下来我们通过合约A来动态调用合约B的功能函数。

合约A

**特别注意：获取合约B的合约哈希后，需要将其反序后再作为参数传入**

```
from ontology.interop.System.App import DynamicAppCall
from ontology.interop.System.Runtime import Notify

def Main(operation, args):
    if operation == "DynamicCallContract":
        if len(args) != 3:
            return False
        revesedContractAddress = args[0]
        opt = args[1]
        params = args[2]
        return DynamicCallContract(revesedContractAddress, opt, params)

    return False

def DynamicCallContract(revesedContractAddress, operation, params):
    res = DynamicAppCall(revesedContractAddress, operation, [params])
    Notify(["the result of the DynamicCall is: ", res])
    return res
```

合约B。也是最简单的Hello World合约。合约A将调用合约B中的Hello函数。

**特别注意：需要先部署合约B，获取合约B的合约哈希**

```
from ontology.interop.System.Runtime import Notify

def Main(operation, args):
    if operation == 'Hello':
        msg = args[0]
        return Hello(msg)

    return False

def Hello(msg):
    return msg
```

## 3. SmartX示例

 1. 部署合约B，得到其合约哈希为`028de32923bcc21e8a5485e4b0e81eff2d9ba08e`.
2. 点击工具栏使用Hex String (Big-endian/Little-endian)，将合约哈希反序得到`8ea09b2dff1ee8b0e485548a1ec2bc2329e38d02`。
3. 部署合约A。
4. 运行DynamicCallContract函数，如图。
![Screen Shot 2019-09-23 at 11.56.19 AM.png](https://upload-images.jianshu.io/upload_images/150344-f52c7b4b07a2ca24.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
5. 点击「运行预执行」，返回`68656c6c6f20776f726c64 ` (hello world十六进制字符串). 调用成功。

## 4. 总结

静态合约调用与动态合约调用的主要区别在于：
* 静态合约调用需要写死合约地址（作为参数写入RegisterAppCall），也就是说静态调用只能调用某个特定合约。而动态合约调用可以将合约地址作为参数传入，意味着可以没有限制的调用任意合约。

* 静态合约调用合约地址无需反序，而动态调用合约地址需要反序。
