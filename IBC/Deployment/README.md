# IBC Deployment Document

This article will detail how to build, deploy, and test an IBC system. Take the Kylin test network and the BOS test network environment as examples.


## IBC 4 libraries
```
https://github.com/boscore/bos.contract-prebuild.git
Https://github.com/boscore/ibc_plugin_eos.git
https://github.com/boscore/ibc_contracts.git
Https://github.com/boscore/ibc_plugin_bos.git
```
## The basic steps
1. build and deploy the eos version
	1. build bosibc.contracts
		1. solution 1 Pre building bosibc.contracts
		1. solution 2 building bosibc.contracts from Source code
			1. build eos
			1. build eosio.cdt
			1. build bosibc.contracts
	1. Deploy bosibc.contracts
	1. Initialize bosibc.contracts
	1. build ibc_plugin_eos
	1. Configure ibc_plugin_eos
1. build and deploy the bos version
	1. build bosibc.contracts
		1. solution 1 Pre building bosibc.contracts
		1. solution 2 building bosibc.contracts from Source code
			1. build bos
			1. build bos.cdt
			1. build bosibc.contracts
	1. Deploy bosibc.contracts
	1. Initialize bosibc.contracts
	1. build ibc_plugin_bos
	1. Configure ibc_plugin_bos
1. IBC cross-chain trading test example

## building and deploying the eos version
### building bosibc.contracts
#### solution 1 Pre-building bosibc.contracts

```
$ git clone https://github.com/boscore/bos.contract-prebuild.git
$ cd bos.contract-prebuild/ibceosio_version
```

#### solution 2 building bosibc.contracts from source code
##### building eos

```
$ git clone https://github.com/EOSIO/eos.git
$ cd eos && git checkout release/v1.5.x
$ ./eosio_build.sh
$ sudo ./eosio_install.sh
```

##### building eosio.cdt
```
$ git clone https://github.com/EOSIO/eosio.cdt.git
$ cd eosio.cdt && git checkout release/v1.4.x
$ ./build.sh
$ sudo ./install.sh
```
##### building bosibc.contracts

``` bash
$ git clone https://github.com/boscore/ibc_contracts.git
$ cd bosibc.contracts
$ ./build.sh
```

### Deploy bosibc.contracts
Create three accounts ibc2chaineos, ibc2tokeneos, ibc2relayeos<br/>
Purchase RAM 10Mb, CPU 100 EOS, NET 100 EOS for ibc2chaineos, ibc2tokeneos<br/>
Purchase CPU 5000 EOS for ibc2relayeos, NET 500 EOS<br/>
And deploy the ibc.chain contract with ibc2chaineos and the ibc.token contract with ibc2tokeneos
### Initialization bosibc.contracts

``` bash

Cleos1=cleos -u http://kylin.fn.eosbixin.com
Contract_chain=ibc2chaineos
Contract_token=ibc2tokeneos

Set the two-chain ibc2tokeneos contract to set eosio.code permissions
$cleos1 set account permission ${contract_token} active '{"threshold": 1, "keys":[{"key":"'${token_c_pubkey}'", "weight":1}], "accounts":[ {"permission":{"actor":"'${contract_token}'","permission":"eosio.code"},"weight":1}], "waits":[] }' owner -p $ {contract_token}

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
### building ibc_plugin_eos

```
$ git clone https://github.com/boscore/ibc_plugin_eos.git
$ cd ibc_plugin_eos
# Comment out #define PLUGIN_TEST on line 39 in the plugins/ibc_plugin/ibc_plugin.cpp file
$ ./eosio_build.sh
```
### Configuring ibc_plugin_eos

Relay full node configuration item

```
Plugin = eosio::ibc::ibc_plugin
Ibc-chain-contract = ibc2chaineos
Ibc-token-contract = ibc2tokeneos
Ibc-relay-name = ibc2relayeos
Ibc-relay-private-key = EOS5jLHvXsFPvUAawjc6qodxUbkBjWcU1j6GUghsNvsGPRdFV5ZWi=KEY:5K2ezP476ThBo9zSrDqTofzaLiKrQaLEkAzv3USdeaFFrD5LAX1
Ibc-listen-endpoint = 0.0.0.0:6001
#ibc-peer-address = 127.0.0.1:6002
Ibc-sidechain-id = aca376f206b8fc25a6ed44dbdc66547c36c6c33e3a119ffbeaef943642f0e906
Ibc-peer-private-key = EOS65jr3UsJi2Lpe9GbxDUmJYUpWeBTJNrqiDq2hYimQyD2kThfAE=KEY:5KHJeTFezCwFCYsaA4Hm2sqEXvxmD2zkgvs3fRT2KarWLiTwv71
```

## build and deploy the bos version
### building bosibc.contracts
#### solution 1 Pre-building bosibc.contracts

```
$ git clone https://github.com/boscore/bos.contract-prebuild.git
$ cd bos.contract-prebuild/ibcbos_version
```

#### solution 2 building bosibc.contracts from Source code
##### build bos
```
$ git clone https://github.com/boscore/bos.git
$ cd eos && git checkout release/v2.0.x
$ ./eosio_build.sh
$ sudo ./eosio_install.sh
```
##### building bos.cdt
```
$ git clone https://github.com/boscore/bos.cdt.git
$ cd bos.cdt && git checkout release/v2.0.x
$ ./build.sh
$ sudo ./install.sh
```
##### building bosibc.contracts

``` bash
$ git clone https://github.com/boscore/ibc_contracts.git
$ cd bosibc.contracts
$ ./build.sh
```
### Deploy bosibc.contracts
Create two accounts ibc2chainbos, ibc2tokenbos, ibc2relaybos<br/>
Buy RAM 10Mb, CPU 100BOS, NET 100 BOS for ibc2chainbos, ibc2tokenbos<br/>
Purchase CPU 5000 BOS for ibc2relaybos, NET 500 BOS<br/>
And deploy the ibc.chain contract on ibc2chainbos on each test network, and deploy the ibc.token contract on ibc2tokenbos
### Initialization bosibc.contracts

``` bash
Cleos2=cleos -u http://47.254.82.241
Contract_chain=ibc2chainbos
Contract_token=ibc2tokenbos

Ibc2tokenbos contract sets eosio.code permissions

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

### building ibc_plugin_bos

``` bash
$ git clone https://github.com/boscore/ibc_plugin_bos.git
$ cd ibc_plugin_bos && git checkout feature/ibc-plugin # In order to test the other functions of bos together, this branch has merged the contents of the master branch.
# Comment out #define PLUGIN_TEST on line 39 in the plugins/ibc_plugin/ibc_plugin.cpp file
$ ./eosio_build.sh
```
### Configuring ibc_plugin_bos
Relay full node configuration item

```
Plugin = eosio::ibc::ibc_plugin
Ibc-chain-contract = ibc2chainbos
Ibc-token-contract = ibc2tokeneos
Ibc-relay-name = ibc2relayeos
Ibc-relay-private-key = EOS5jLHvXsFPvUAawjc6qodxUbkBjWcU1j6GUghsNvsGPRdFV5ZWi=KEY:5K2ezP476ThBo9zSrDqTofzaLiKrQaLEkAzv3USdeaFFrD5LAX1
Ibc-listen-endpoint = 0.0.0.0:6001
#ibc-peer-address = 127.0.0.1:6002
Ibc-sidechain-id = aca376f206b8fc25a6ed44dbdc66547c36c6c33e3a119ffbeaef943642f0e906
Ibc-peer-private-key = EOS65jr3UsJi2Lpe9GbxDUmJYUpWeBTJNrqiDq2hYimQyD2kThfAE=KEY:5KHJeTFezCwFCYsaA4Hm2sqEXvxmD2zkgvs3fRT2KarWLiTwv71
```


## IBC Cross-Chain Transaction Test Example

After the configuration is started, the node is initialized, and after the parties' contracts are initialized, and the first section is completed, cross-chain transactions can be performed.

### Contract name

- eos relay contract account: ibctoken2bos
- Bos's contract account: ibctoken2eos

## Detailed operation

The urls of the two networks that need to be used:<br/>
Kylin-api= -u http://kylin.meet.one:8888<br/>
Bos-api= -u http://bos-testnet.meet.one:8888

### 1) Transfer "50.0000 EOS" from kylin test network to BOS test online
````
Cleos ${kylin-api} transfer ibctoken2bos "10.0000 EOS" "boscoretest2@bos notes infomation" -p ibckylintest
Cleos ${kylin-api} get currency balance eosio.token ibckylintest # reduction
Cleos ${kylin-api} get currency balance eosio.token ibctoken2bos #增
````
View on the BOS test online
```
$cleos ${bos-api} get currency balance ibctoken2eos boscoretest2
100.0000 EOSPG
```

### 2) From the BOS test network, transfer "50.0000 BOS" to kylin test online
```
Cleos ${bos-api} transfer boscoretest2 ibctoken2eos "50.0000 BOS" "boscoretest2@eos notes infomation" -p ibckylintest
Cleos ${kylin-api} transfer "10.0000 EOS"
Cleos ${bos-api} get currency balance eosio.token boscoretest2 # reduction
Cleos ${bos-api} get currency balance eosio.token ibctoken2eos #增
```
View on kylin test network
```
$cleos ${kylin-api} get currency balance ibctoken2bos ibckylintest
50.0000 BOSPG
```

### 3) Transfer "10.0000 BOSPG" from kylin test network to BOS test network
````
Cleos ${kylin-api} push action ibctoken2bos transfer '["ibckylintest","ibctoken2bos","10.0000 BOSPG" "boscoretest2@bos notes infomation"]' -p ibckylintest
Cleos ${kylin-api} get currency balance ibctoken2bos ibckylintest #min 10 BOSPS
````
View on the BOS test online
```
$cleos ${bos-api} get currency balance eosio.token boscoretest2 #增 10 BOS
```

### 4) Transfer "10.0000 EOSPG" from the BOS test network to kylin test network
````
Cleos ${bos-api} push action ibctoken2eos transfer '["boscoretest2","ibctoken2eos","10.0000 EOSPG" "ibckylintest@eos notes infomation"]' -p boscoretest2
Cleos ${bos-api} get currency balance ibctoken2eos boscoretest2 #reduce 10 BOSPS
````
View on kylin test online
```
$cleos ${kylin-api} get currency balance eosio.token ibckylintest #增 10 EOS
```

*Note: Due to the cross-chain transfer, there will be a delay in the arrival time*