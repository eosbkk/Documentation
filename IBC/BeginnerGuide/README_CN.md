# 测试网使用新手指南
1. IBC相关概念
1. IBC部署环境
1. IBC参数初始化
1. 测试网测试示例

## IBC相关概念
### 中继节点
中继节点位于IBC连接两个eos生态链的两端，每一端至少一个以上中继节点，通过两端中继节点建立两条链通信。
### 中继合约
中继合约有IBC.token, IBC.chain两个合约分别部署到需要IBC的两条链上。
ibc.token 主要用于代币注册及管理，
ibc.chain 主要用于两条链中继节点通信使用。
### 中继合约账户
中继合约账户用于部署中继合约ibc.token,ibc.chain.如：
* eos的中继合约账户： ibctoken2bos
* bos的中继合约账户： ibctoken2eos
### 注册代币
需要IBC的代币通过中继合约ibc.token注册，初始化，就可以进行IBC。
## IBC部署环境
### 中继节点部署
按照eos主链或侧链节点要求，获取源码添加ibc plugin插件。
（详细信息参见[部署文档](https://github.com/vlbos/Documentation-1/blob/master/IBC/Deployment/README_CN.md)）
### 中继合约部署
按照部署文档获取预编译合约文件或从源码编译合约。
创建中继合约账户部署
（详细信息参见[部署文档](https://github.com/vlbos/Documentation-1/blob/master/IBC/Deployment/README_CN.md)）
## IBC参数初始化
设置IBC 基本参数，进行初始值设置。如注册代币token.
### 注册token
注册token操作步骤如下（详细信息参见[注册token注册及管理文档](https://github.com/vlbos/Documentation-1/blob/master/IBC/Token_Registration_and_Management.md)）：
下面的两步骤都是token合约操作的，如果想要注册token，请联系我们并且提供相关的信息。

#### 在kylin测试网上，通过中继合约进行token的注册
```
#cleos ${kylin-api}  push action ibctoken2bos regacpttoken   '["<token-contract>","<max_accept>","<admin-account>","<min_once_transfer>","<max_once_transfer>", "<max_once_transfer>",<max_tfs_per_minute>,"<organization>","<website>","<service_fee_mode>","<service_fee_fixed>",<service_fee_ratio>,"<failed_fee_mode>","<failed_fee_fixed>",<failed_fee_ratio>,<active>,"<peerchain_sym>"]' -p ibctoken2bos
例如：
cleos ${kylin-api}  push action ibctoken2bos regacpttoken   '["eostoretoken","1000000000.0000 EST","eosstoreeost","5.0000 EST","500.0000 EST", "100000.0000 EST",1000,"eos store","www.eosstore.com","fixed","0.1000 EST",0.01,"fixed","0.1000 EST",0.01,true,"4,EST"]' -p ibctoken2bos
```
#### 在bos测试网上注册锚定的token的信息

```
#cleos  ${bos-api}  push action ibctoken2eos regpegtoken  '["<max_supply>","<min_once_withdraw>","<max_once_withdraw>", "<max_daily_withdraw>",<max_wds_per_minute>,"<administrator>","<peerchain_contract>","<peerchain_sym>","<failed_fee_mode>","<failed_fee_fixed>",<failed_fee_ratio>,<active>]' -p ibctoken2eos
例如：
cleos  ${bos-api}  push action ibctoken2eos regpegtoken  '["1000000000.0000 EST","10.0000 EST","5000.0000 EST", "100000.0000 EST",1000,"bosstoreeost","eostoretoken","4,EST","fixed","0.1000 EST",0.01,true]' -p ibctoken2eos
```
新增加的token转账就如上面写的eos转账是一样

## 测试网测试示例

需要用的两个网络的url：

kylin-api= -u http://kylin.meet.one:8888

bos-api= -u http://bos-testnet.meet.one:8888

### 1) 从kylin测试网上转出"50.0000 EOS"到BOS测试网上
````
cleos ${kylin-api}  transfer  ibctoken2bos  "10.0000 EOS" "boscoretest2@bos notes infomation" -p  ibckylintest
cleos ${kylin-api} get currency balance  eosio.token ibckylintest #减少
cleos ${kylin-api} get currency balance  eosio.token ibctoken2bos #增加 
````
在BOS测试网上查看
```
$cleos ${bos-api} get currency balance  ibctoken2eos boscoretest2
100.0000 EOSPG
```

### 2) 从BOS测试网上转出"10.0000 EOSPG"到kylin测试网
````
cleos ${bos-api} push action ibctoken2eos transfer '["boscoretest2","ibctoken2eos","10.0000 EOSPG" "ibckylintest@eos notes infomation"]' -p boscoretest2   
cleos ${bos-api} get currency balance ibctoken2eos boscoretest2 #减少10 BOSPS
````
在kylin测试网上查看
```
$cleos ${kylin-api} get currency balance  eosio.token ibckylintest #增加 10 EOS
```
### 3) 从BOS测试网上boscoretest2 转给boscoretest1 "10.0000 EOSPG"
````
cleos ${bos-api} push action ibctoken2eos transfer '["boscoretest2","boscoretest1","10.0000 EOSPG" "transfer"]' -p boscoretest2   
cleos ${bos-api} get currency balance ibctoken2eos boscoretest2 #减少10 BOSPS
cleos ${bos-api} get currency balance ibctoken2eos boscoretest1 #增加10 BOSPS
````


### 4) 从BOS测试网上，转出“50.0000 BOS”到kylin测试网上
```
cleos ${bos-api} transfer boscoretest2  ibctoken2eos "50.0000 BOS" "boscoretest2@eos notes infomation" -p  ibckylintest
cleos ${bos-api} get currency balance  eosio.token boscoretest2 #减少
cleos ${bos-api} get currency balance  eosio.token ibctoken2eos #增加 
```
在kylin测试网上进行查看
```
$cleos ${kylin-api} get currency balance ibctoken2bos ibckylintest
50.0000 BOSPG
```

### 5) 从kylin测试网上转出"10.0000 BOSPG"到BOS测试网
````
cleos ${kylin-api} push action ibctoken2bos transfer '["ibckylintest","ibctoken2bos","10.0000 BOSPG" "boscoretest2@bos notes infomation"]' -p ibckylintest   
cleos ${kylin-api} get currency balance ibctoken2bos ibckylintest #减少10 BOSPS
````
在BOS测试网上查看
```
$cleos ${bos-api} get currency balance  eosio.token boscoretest2 #增加 10 BOS
```

### 6) 从BOS测试网上ibckylintest 转给ibckylintesa "10.0000 EOSPG"
````
cleos ${kylin-api} push action ibctoken2bos transfer '["ibckylintest","ibckylintesa","10.0000 EOSPG" "transfer"]' -p ibckylintest  
cleos ${kylin-api} get currency balance ibctoken2bos ibckylintest #减少10 EOSPG
cleos ${kylin-api} get currency balance ibctoken2bos ibckylintesa #增加10 EOSPG
````

*说明：由于进行的跨链转账，所以到账时间会有延迟*












