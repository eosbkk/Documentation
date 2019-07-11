


# IBC 部署文档

本文将详细描述如何编译、部署、测试IBC系统。以Kylin 测试网 和 BOS 测试网 环境为例


## IBC 4个库
```
https://github.com/boscore/bos.contract-prebuild.git
https://github.com/boscore/ibc_plugin_eos.git
https://github.com/boscore/ibc_contracts.git
https://github.com/boscore/ibc_plugin_bos.git
```
## 基本步骤
1. 编译部署eos版本
   1. 编译 bosibc.contracts
		1. 方案一 预编译 bosibc.contracts
		1. 	方案二 源码编译 bosibc.contracts
			1. 编译 eos
			1. 	编译 eosio.cdt
			1. 	编译 bosibc.contracts
	1. 部署 bosibc.contracts
	1. 初始化 bosibc.contracts
	1. 编译 ibc_plugin_eos
	1. 配置  ibc_plugin_eos
1. 编译部署bos版本
   1. 编译 bosibc.contracts
		1. 方案一 预编译 bosibc.contracts
		1. 方案二 源码编译 bosibc.contracts
			1. 	编译 bos
			1. 	编译 bos.cdt
			1. 	编译 bosibc.contracts
	1. 部署 bosibc.contracts
	1. 初始化 bosibc.contracts
	1. 编译 ibc_plugin_bos
	1. 配置  ibc_plugin_bos
1. IBC跨链交易测试示例

## 编译部署eos版本

### 编译 bosibc.contracts
#### 方案一 预编译 bosibc.contracts

```
$ git clone https://github.com/boscore/bos.contract-prebuild.git
$ cd bos.contract-prebuild/ibceosio_version
```

#### 方案二 源码编译 bosibc.contracts
##### 编译 eos

``` 
$ git clone https://github.com/EOSIO/eos.git
$ cd eos && git checkout release/v1.5.x
$ ./eosio_build.sh
$ sudo ./eosio_install.sh
```

##### 编译 eosio.cdt
``` 
$ git clone https://github.com/EOSIO/eosio.cdt.git
$ cd eosio.cdt && git checkout release/v1.4.x
$ ./build.sh
$ sudo ./install.sh
```
##### 编译 bosibc.contracts

``` bash
$ git clone https://github.com/boscore/ibc_contracts.git
$ cd bosibc.contracts 
$ ./build.sh
```

### 部署 bosibc.contracts
创建三个账号 ibc2chaineos, ibc2tokeneos, ibc2relayeos<br/>
为 ibc2chaineos, ibc2tokeneos 购买 RAM 10Mb, CPU 100 EOS, NET 100 EOS<br/>
为 ibc2relayeos 购买 CPU 5000 EOS, NET 500 EOS<br/>
并 用ibc2chaineos 部署ibc.chain 合约， 用ibc2tokeneos 部署ibc.token合约
### 初始化 bosibc.contracts

``` bash

cleos1=cleos -u http://kylin.fn.eosbixin.com
contract_chain=ibc2chaineos
contract_token=ibc2tokeneos

把两个链的ibc2tokeneos合约设置eosio.code权限
$cleos1 set account permission ${contract_token} active '{"threshold": 1, "keys":[{"key":"'${token_c_pubkey}'", "weight":1}], "accounts":[{"permission":{"actor":"'${contract_token}'","permission":"eosio.code"},"weight":1}], "waits":[] }' owner -p ${contract_token}

$cleos1 push action ${contract_chain} setglobal '[{"lib_depth":170}]' -p ${contract_chain}
$cleos1 push action ${contract_chain} relay '["add","ibc2relayeos"]' -p ${contract_chain}
$cleos1 push action ${contract_token} setglobal '["ibc2chaineos","ibc2tokeneos",5000,1000,10,true]' -p ${contract_token}
$cleos1 push action ${contract_token} regacpttoken \
    '["eosio.token","1000000000.0000 EOS","ibc2tokeneos","10.0000 EOS","5000.0000 EOS",
    "100000.0000 EOS",1000,"organization-name","www.website.com","fixed","0.1000 EOS",0.01,true,"4,EOSPG"]' -p ${contract_token}
$cleos1 push action ${contract_token} regpegtoken \
    '["1000000000.0000 BOSPG","10.0000 BOSPG","5000.0000 BOSPG",
    "100000.0000 BOSPG",1000,"ibc2tokeneos","eosio.token","4,BOS",true]' -p ${contract_token}

```
### 编译 ibc_plugin_eos

``` 
$ git clone https://github.com/boscore/ibc_plugin_eos.git
$ cd ibc_plugin_eos
#注释掉 plugins/ibc_plugin/ibc_plugin.cpp 文件中约第39行的 #define PLUGIN_TEST
$ ./eosio_build.sh
```
### 配置  ibc_plugin_eos

中继全节点配置项

``` 
plugin = eosio::ibc::ibc_plugin
ibc-chain-contract = ibc2chaineos
ibc-token-contract = ibc2tokeneos
ibc-relay-name = ibc2relayeos
ibc-relay-private-key = EOS5jLHvXsFPvUAawjc6qodxUbkBjWcU1j6GUghsNvsGPRdFV5ZWi=KEY:5K2ezP476ThBo9zSrDqTofzaLiKrQaLEkAzv3USdeaFFrD5LAX1
ibc-listen-endpoint = 0.0.0.0:6001
#ibc-peer-address = 127.0.0.1:6002
ibc-sidechain-id = aca376f206b8fc25a6ed44dbdc66547c36c6c33e3a119ffbeaef943642f0e906
ibc-peer-private-key = EOS65jr3UsJi2Lpe9GbxDUmJYUpWeBTJNrqiDq2hYimQyD2kThfAE=KEY:5KHJeTFezCwFCYsaA4Hm2sqEXvxmD2zkgvs3fRT2KarWLiTwv71
```

## 编译部署bos版本
### 编译 bosibc.contracts
#### 方案一 预编译 bosibc.contracts

```
$ git clone https://github.com/boscore/bos.contract-prebuild.git
$ cd bos.contract-prebuild/ibcbos_version
```

#### 方案二 源码编译 bosibc.contracts
##### 编译 bos
``` 
$ git clone https://github.com/boscore/bos.git
$ cd eos && git checkout release/v2.0.x
$ ./eosio_build.sh
$ sudo ./eosio_install.sh
```
##### 编译 bos.cdt
``` 
$ git clone https://github.com/boscore/bos.cdt.git
$ cd bos.cdt && git checkout release/v2.0.x
$ ./build.sh
$ sudo ./install.sh
```
##### 编译 bosibc.contracts

``` bash
$ git clone https://github.com/boscore/ibc_contracts.git
$ cd bosibc.contracts 
$ ./build.sh
```
### 部署 bosibc.contracts
创建两个账号 ibc2chainbos, ibc2tokenbos, ibc2relaybos<br/>
为 ibc2chainbos, ibc2tokenbos 购买 RAM 10Mb, CPU 100BOS, NET 100 BOS<br/>
为 ibc2relaybos 购买 CPU 5000 BOS, NET 500 BOS<br/>
并在各个测试网的 ibc2chainbos 部署ibc.chain 合约， 在 ibc2tokenbos 部署ibc.token合约
### 初始化 bosibc.contracts

``` bash
cleos2=cleos -u http://47.254.82.241
contract_chain=ibc2chainbos
contract_token=ibc2tokenbos

ibc2tokenbos合约设置eosio.code权限

$cleos2 set account permission ${contract_token} active '{"threshold": 1, "keys":[{"key":"'${token_c_pubkey}'", "weight":1}], "accounts":[{"permission":{"actor":"'${contract_token}'","permission":"eosio.code"},"weight":1}], "waits":[] }' owner -p ${contract_token}

$cleos2 push action ${contract_chain} setglobal '[{"lib_depth":170}]' -p ${contract_chain}
$cleos2 push action ${contract_chain} relay '["add","ibc2relayeos"]' -p ${contract_chain}
$cleos2 push action ${contract_token} setglobal '["ibc2chainbos","ibc2tokenbos",5000,1000,10,true]' -p ${contract_token}
$cleos2 push action ${contract_token} regacpttoken \
    '["eosio.token","1000000000.0000 BOS","ibc2tokenbos","10.0000 BOS","5000.0000 BOS",
    "100000.0000 BOS",1000,"organization-name","www.website.com","fixed","0.1000 BOS",0.01,true,"4,BOSPG"]' -p ${contract_token}
$cleos2 push action ${contract_token} regpegtoken \
    '["1000000000.0000 EOSPG","10.0000 EOSPG","5000.0000 EOSPG",
    "100000.0000 EOSPG",1000,"ibc2tokenbos","eosio.token","4,EOS",true]' -p ${contract_token}

```

### 编译 ibc_plugin_bos

``` bash
$ git clone https://github.com/boscore/ibc_plugin_bos.git
$ cd ibc_plugin_bos && git checkout feature/ibc-plugin   # 为了结合bos其他功能一起测试，此分支已经合并了master分支的内容
# 注释掉 plugins/ibc_plugin/ibc_plugin.cpp 文件中约第39行的 #define PLUGIN_TEST
$ ./eosio_build.sh
```
### 配置  ibc_plugin_bos
中继全节点配置项

``` 
plugin = eosio::ibc::ibc_plugin
ibc-chain-contract = ibc2chainbos
ibc-token-contract = ibc2tokeneos
ibc-relay-name = ibc2relayeos
ibc-relay-private-key = EOS5jLHvXsFPvUAawjc6qodxUbkBjWcU1j6GUghsNvsGPRdFV5ZWi=KEY:5K2ezP476ThBo9zSrDqTofzaLiKrQaLEkAzv3USdeaFFrD5LAX1
ibc-listen-endpoint = 0.0.0.0:6001
#ibc-peer-address = 127.0.0.1:6002
ibc-sidechain-id = aca376f206b8fc25a6ed44dbdc66547c36c6c33e3a119ffbeaef943642f0e906
ibc-peer-private-key = EOS65jr3UsJi2Lpe9GbxDUmJYUpWeBTJNrqiDq2hYimQyD2kThfAE=KEY:5KHJeTFezCwFCYsaA4Hm2sqEXvxmD2zkgvs3fRT2KarWLiTwv71
```


## IBC跨链交易测试示例

配置好后启动节点，等各方合约都初始化，并完成第一个section之后，可以进行跨链交易

### 合约名字

- eos的中继合约账户： ibctoken2bos
- bos的中继合约账户： ibctoken2eos

## 详细操作

需要用的两个网络的url：<br/>
kylin-api= -u http://kylin.meet.one:8888<br/>
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

### 2) 从BOS测试网上，转出“50.0000 BOS”到kylin测试网上
```
cleos ${bos-api} transfer boscoretest2  ibctoken2eos "50.0000 BOS" "boscoretest2@eos notes infomation" -p  ibckylintest
cleos ${kylin-api}  transfer    "10.0000 EOS" 
cleos ${bos-api} get currency balance  eosio.token boscoretest2 #减少
cleos ${bos-api} get currency balance  eosio.token ibctoken2eos #增加 
```
在kylin测试网上进行查看
```
$cleos ${kylin-api} get currency balance ibctoken2bos ibckylintest
50.0000 BOSPG
```

### 3) 从kylin测试网上转出"10.0000 BOSPG"到BOS测试网
````
cleos ${kylin-api} push action ibctoken2bos transfer '["ibckylintest","ibctoken2bos","10.0000 BOSPG" "boscoretest2@bos notes infomation"]' -p ibckylintest   
cleos ${kylin-api} get currency balance ibctoken2bos ibckylintest #减少10 BOSPS
````
在BOS测试网上查看
```
$cleos ${bos-api} get currency balance  eosio.token boscoretest2 #增加 10 BOS
```

### 4) 从BOS测试网上转出"10.0000 EOSPG"到kylin测试网
````
cleos ${bos-api} push action ibctoken2eos transfer '["boscoretest2","ibctoken2eos","10.0000 EOSPG" "ibckylintest@eos notes infomation"]' -p boscoretest2   
cleos ${bos-api} get currency balance ibctoken2eos boscoretest2 #减少10 BOSPS
````
在kylin测试网上查看
```
$cleos ${kylin-api} get currency balance  eosio.token ibckylintest #增加 10 EOS
```

*说明：由于进行的跨链转账，所以到账时间会有延迟*







