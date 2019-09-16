<h1 align="center">本体智能合约语法专辑(7) - Upgrade API</h1>

## 1. 导语

上一期我们介绍了合约原生API，讲述了如何利用智能合约进行ONT/ONG转账。本期我们讨论如何通过Upgrade API来进行合约升级。合约升级共有2个API，分别为Destroy和Migrate，用法如下：

| API                          | 返回值  | 描述                                      |
| ---------------------------- | ---- | ---------------------------------------- |
| Destroy                 | none |      销毁合约      |
| Migrate | contract structure | 迁移合约. 旧合约将会被新合约取代  |

下面我们具体讲述一下这两个 API 的使用方法。在这之前，小伙伴们可以在本体智能合约开发工具 SmartX 中新建一个合约，跟着我们进行操作。同样，在文章最后我们将给出这次讲解的所有源代码以及视频讲解。
所有语法部分提供中英文视频。中文视频地址：[http://v.qq.com/vplus/c050cba4cae82ec985fc9e995a19b595?page=cover](http://v.qq.com/vplus/c050cba4cae82ec985fc9e995a19b595?page=cover)

## 2. Upgrade API使用方法

### 引用

```
from ontology.interop.Ontology.Contract import Migrate
from ontology.interop.System.Contract import Destroy
```

### Destroy API

Destroy API 用于销毁合约，旧合约将会在链上被删除。

#### Example

```
from ontology.interop.System.Contract import Destroy
from ontology.interop.System.Runtime import Notify

def Main(operation, args):
    if operation == "destroy_contract":
        return destroy_contract()
    return False

def destroy_contract():
    Destroy() # 调用destroy 销毁此合约
    Notify(["The contract has been destoryed"])
    return True
```

#### SmartX示例

1. 将以上代码粘贴至SmartX编译并部署。

2. 再次点击部署会弹出「合约部署失败，该合约已经部署过」。因为链上已经存在相同合约。

![Screen Shot 2019-09-16 at 10.58.07 AM.png](https://upload-images.jianshu.io/upload_images/150344-a6a6ff406ff5c5cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. 运行destroy_contract函数销毁合约

![Screen Shot 2019-09-16 at 11.01.36 AM.png](https://upload-images.jianshu.io/upload_images/150344-85659f4a5c4e95d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4. 再次点击部署合约，会发现合约可以再次部署，不会再弹出「合约部署失败，该合约已经部署过」。证明原先合约已经在链上被销毁。

### Migrate API 

Migrate API 用于迁移合约， 旧合约将会被新合约取代，旧合约中的数据也将自动迁移到新合约。迁移数据量越大，迁移费用越高。迁移成功后，旧合约会被删除。

**特别注意：合约中的资产不会被自动迁移，需要提前转走，否则将无法取回，相当于转入黑洞地址**

#### Migrate 参数

| 参数                         | 描述                                       |
| ----------------------------  | ---------------------------------------- |
|script| avm字节码|
|need_storage| 数据是否需要存储 |
|name| 合约名称|
|version| 合约版本|
|author| 合约作者 |
|email| 邮箱 |
|description| 合约描述|

#### Example

```
from ontology.interop.Ontology.Contract import Migrate
from ontology.interop.System.Runtime import Notify
from ontology.libont import AddressFromVmCode

def Main(operation, args):
    if operation == "migrate_contract":
        if len(args) != 7:
            return False
        avm_code = args[0]
        need_storage = args[1]
        name = args[2]
        version = args[3]
        author = args[4]
        email = args[5]
        description = args[6]
        return migrate_contract(avm_code, need_storage, name, version, author, email, description)

    return False

def migrate_contract(avm_code, need_storage, name, version, author, email, description):
    res = Migrate(avm_code, need_storage, name, version, author, email, description) # 调用Migrate 迁移此合约
    if res:
        Notify(["Migrate successfully"])
        new_contract_hash=AddressFromVmCode(avm_code) # 计算新合约地址
        Notify(new_contract_hash) # 打印出新合约地址
        return True
    else:
        return False
```

#### SmartX示例

1. 将以上代码粘贴至SmartX编译，填入参数

**特别注意：1. 要确认avm_code在链上不存在，不然会报错。2.Migrate需要较高的gas limit，所以运行函数时要调整gas limit**

![Screen Shot 2019-09-16 at 11.30.00 AM.png](https://upload-images.jianshu.io/upload_images/150344-0258fcd288ce3ff3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 部署成功后，可以从控制台拿到新的合约哈希。be4606c4663081b70f745ed9fc64d4c3b0d9c183 为新合约哈希。

![Screen Shot 2019-09-16 at 11.55.20 AM.png](https://upload-images.jianshu.io/upload_images/150344-c056edae8ce3bc05.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


更详细的操作步骤请见视频。

### 本期问答：

迁移合约时，「 」将会被自动迁移，「 」将不会被自动迁移。

A. 合约资产，合约数据
B. 合约数据，合约资产 
C. 合约中的币，合约代码
D. 合约数据，合约代码



