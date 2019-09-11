<h1 align="center">本体智能合约语法专辑(6) - Native API</h1>

## 1. 导语

上一期我们介绍了合约执行API，本期我们讨论如何通过Native API来进行本体原生合约调用。原生合约调用最典型的功能就是合约转账，也是整个智能合约最核心的部分，本期我们就来尝试一下合约转账。 Native API 只有1个API。用法如下：

| API                          | 返回值  |描述                                       |
| ---------------------------- | ---- | ---------------------------------------- |
| Invoke                 | byte[] |  调用原生合约    |

同时，使用invoke函数需要state函数辅助来封装参数，用法如下:
| API                          | 返回值  |描述                                       |
| ---------------------------- | ---- | ---------------------------------------- |
| state | string |      封装转账参数             |

下面我们具体讲述一下这两个 API 的使用方法。在这之前，小伙伴们可以在本体智能合约开发工具 SmartX 中新建一个合约，跟着我们进行操作。同样，在文章最后我们将给出这次讲解的所有源代码以及视频讲解。
所有语法部分提供中英文视频。中文视频地址：[https://v.qq.com/x/page/r0830erkp8b.html](https://v.qq.com/x/page/r0830erkp8b.html)


## 2. Native API 使用方法

### 引用 

```
from ontology.interop.Ontology.Native import Invoke
from ontology.builtins import state
```

### 本体原生合约列表

以下是可以使用Native API调用的原生合约列表。在合约中，将合约地址转成bytearray形式传入Invoke即可。例如：
```
contract_address_ONT = bytearray(b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x01')
param = state(from_acct, to_acct, ont_amount) # 参数为转出地址，转入地址， 转账金额
res = Invoke(1, contract_address_ONT, 'transfer', [param])
```

|合约名称 | 合约地址 | 
---|---|
|ONT Token 合约 | 0000000000000000000000000000000000000001| 
|ONG Token 合约 | 0000000000000000000000000000000000000002 | 
|ONT ID 合约 | 0000000000000000000000000000000000000003 | 
|Global Params | 0000000000000000000000000000000000000004 | 
|Oracle | 0000000000000000000000000000000000000005 | 
|Authorization Manager(Auth) | 0000000000000000000000000000000000000006 | 
|Governance | 0000000000000000000000000000000000000007 | 
|DDXF(Decentralized Exchange) | 0000000000000000000000000000000000000008 |

### 转账合约代码

```
from ontology.interop.System.Runtime import Notify, CheckWitness
from ontology.interop.Ontology.Runtime import Base58ToAddress
from ontology.interop.Ontology.Native import Invoke
from ontology.builtins import state

# contract address 
contract_address_ONT = bytearray(b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x01')
contract_address_ONG = bytearray(b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02')

def Main(operation, args):
    if operation == 'transfer':
        from_acct = args[0]
        to_acct = args[1]
        ont_amount = args[2]
        ong_amount = args[3]
        return transfer(from_acct,to_acct,ont_amount,ong_amount)
    
    return False


def transfer(from_acct, to_acct, ont_amount, ong_amount):
    # 将base58地址转换成 bytearray格式地址 
    from_acct=Base58ToAddress(from_acct)
    to_acct=Base58ToAddress(to_acct)
    # 验签，调用方必须与转出地址为同一地址
    if CheckWitness(from_acct):
        # ONT转账
        if ont_amount > 0:
            param = state(from_acct, to_acct, ont_amount) # state函数用于封装转账相关参数
            res = Invoke(1, contract_address_ONT, 'transfer', [param]) # invoke调用ONT token原生合约转账
            if res and res == b'\x01':
                Notify('transfer succeed')
            else:
                Notify('transfer failed')
        # ONG转账，流程同上
        if ong_amount > 0:
            param = state(from_acct, to_acct, ong_amount)
            res = Invoke(1, contract_address_ONG, 'transfer', [param])
            if res and res == b'\x01':
                Notify('transfer succeed')
            else:
                Notify('transfer failed')
    else:
        Notify('CheckWitness failed')
```

合约代码流程如下：
1. 定义合约地址变量contract_address_ONT，contract_address_ONG
2. 将转出地址，转入地址从base58格式转成bytearray格式
3. 验签，确认转出地址与合约调用地址为同一地址
4. state函数封装转账相关参数
5. invoke调用ONT token原生合约转账，参数分别为版本号，合约地址，调用的合约方法，state封装的转账相关参数
6. 通过返回res判断转账是否成功，b'\x01'为成功，成功输出“transfer succeed”。

### smartx实践

1. 编译合约
![compile](https://upload-images.jianshu.io/upload_images/150344-73b29d22dc20bd99.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 部署合约
如需申请测试币，申请地址为[https://developer.ont.io/applyOng](https://developer.ont.io/applyOng)

![deploy](https://upload-images.jianshu.io/upload_images/150344-56168736d87d248e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. 执行转账

转账参数设置
![transfer](https://upload-images.jianshu.io/upload_images/150344-5c17e4bce9529dfc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

转账成功
![Screen Shot 2019-09-11 at 5.16.06 PM.png](https://upload-images.jianshu.io/upload_images/150344-e850cfa96ed8b2d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 5. 本期问答

合约转账时，若想转2个ONT和3.12个ONG，转账数量参数分别为：
A. 2，3120000000
B. 2000000000， 3.12
C. 200， 312
D. 2， 312



