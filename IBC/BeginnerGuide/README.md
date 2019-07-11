# TestNet Beginner's Guide
1. IBC related concepts
1. IBC deployment environment
1. IBC parameter initialization
1. Test network test example

## IBC related concepts
### Relay node
The relay node is located at both ends of the two eos ecological chains connected by the IBC, and at least one relay node at each end establishes two chain communication through the relay nodes at both ends.
### Relay contract
The relay contract has ibc.token, ibc.chain two contracts are deployed to the two chains that need IBC.
Ibc.token is mainly used for token registration and management.
Ibc.chain is mainly used for communication between two chain relay nodes.
### Relay contract account
The trunk contract account is used to deploy the trunk contract ibc.token, ibc.chain.
* eos relay contract account: ibctoken2bos
* bos relay contract account: ibctoken2eos
### Registering tokens
IBC tokens are required to be registered and initialized by the relay contract ibc.token, and IBC can be performed.
## IBC Deployment Environment
### Relay node deployment
According to the requirements of the eos main chain or side chain node, get the source code to add the ibc plugin plugin.
(See the [deployment documentation](https://github.com/vlbos/Documentation-1/blob/master/IBC/Deployment/README.md) for details)
### Trunk contract deployment
Obtain the precompiled contract file according to the deployment documentation or compile the contract from source.
Create a trunk contract account deployment
(See the [deployment documentation](https://github.com/vlbos/Documentation-1/blob/master/IBC/Deployment/README.md) for details)
## IBC parameter initialization
Set the IBC basic parameters and set the initial values. Such as registering tokens token.
### Register token
The procedure for registering the token is as follows (see Registering the [Token Registration and Management Document](https://github.com/vlbos/Documentation-1/blob/master/IBC/Token_Registration_and_Management.md) for details):
The following two steps are all operated on the token contract. If you want to register the token, please contact us and provide relevant information.

#### On the kylin test network, register the token through the relay contract
```
#cleos ${kylin-api} push action ibctoken2bos regacpttoken '["<token-contract>","<max_accept>","<admin-account>","<min_once_transfer>","<max_once_transfer>", "< Max_once_transfer>",<max_tfs_per_minute>,"<organization>","<website>","<service_fee_mode>","<service_fee_fixed>",<service_fee_ratio>,"<failed_fee_mode>","<failed_fee_fixed>",<failed_fee_ratio >,<active>,"<peerchain_sym>"]' -p ibctoken2bos
E.g:
Cleos ${kylin-api} push action ibctoken2bos regacpttoken '["eostoretoken","1000000000.0000 EST","eosstoreeost","5.0000 EST","500.0000 EST", "100000.0000 EST",1000,"eos store","www .eosstore.com","fixed","0.1000 EST",0.01,"fixed","0.1000 EST",0.01,true,"4,EST"]' -p ibctoken2bos
```
#### Registering anchored token information on the bos test web

```
#cleos ${bos-api} push action ibctoken2eos regpegtoken '["<max_supply>","<min_once_withdraw>","<max_once_withdraw>", "<max_daily_withdraw>",<max_wds_per_minute>,"<administrator>","< Peerchain_contract>","<peerchain_sym>","<failed_fee_mode>","<failed_fee_fixed>",<failed_fee_ratio>,<active>]' -p ibctoken2eos
E.g:
Cleos ${bos-api} push action ibctoken2eos regpegtoken '["1000000000.0000 EST","10.0000 EST","5000.0000 EST", "100000.0000 EST",1000,"bosstoreeost","eostoretoken","4,EST"," Fixed","0.1000 EST",0.01,true]' -p ibctoken2eos
```
The newly added token transfer is the same as the eos transfer written above.

##Test Network Test Example

The urls of the two networks that need to be used:

Kylin-api= -u http://kylin.meet.one:8888

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

### 2) Transfer "10.0000 EOSPG" from the BOS test network to kylin test network
````
Cleos ${bos-api} push action ibctoken2eos transfer '["boscoretest2","ibctoken2eos","10.0000 EOSPG" "ibckylintest@eos notes infomation"]' -p boscoretest2
Cleos ${bos-api} get currency balance ibctoken2eos boscoretest2 #reduce 10 BOSPS
````
View on kylin test online
```
$cleos ${kylin-api} get currency balance eosio.token ibckylintest #增 10 EOS
```
### 3) Transfer from BOS test online boscoretest2 to boscoretest1 "10.0000 EOSPG"
````
Cleos ${bos-api} push action ibctoken2eos transfer '["boscoretest2","boscoretest1","10.0000 EOSPG" "transfer"]' -p boscoretest2
Cleos ${bos-api} get currency balance ibctoken2eos boscoretest2 #reduce 10 BOSPS
Cleos ${bos-api} get currency balance ibctoken2eos boscoretest1 #增10 BOSPS
````


### 4) From the BOS test online, transfer "50.0000 BOS" to kylin test online
```
Cleos ${bos-api} transfer boscoretest2 ibctoken2eos "50.0000 BOS" "boscoretest2@eos notes infomation" -p ibckylintest
Cleos ${bos-api} get currency balance eosio.token boscoretest2 # reduction
Cleos ${bos-api} get currency balance eosio.token ibctoken2eos #增
```
View on kylin test network
```
$cleos ${kylin-api} get currency balance ibctoken2bos ibckylintest
50.0000 BOSPG
```

### 5) Transfer "10.0000 BOSPG" from kylin test network to BOS test network
````
Cleos ${kylin-api} push action ibctoken2bos transfer '["ibckylintest","ibctoken2bos","10.0000 BOSPG" "boscoretest2@bos notes infomation"]' -p ibckylintest
Cleos ${kylin-api} get currency balance ibctoken2bos ibckylintest #min 10 BOSPS
````
View on the BOS test online
```
$cleos ${bos-api} get currency balance eosio.token boscoretest2 #增 10 BOS
```

### 6) Transfer from the BOS test online ibckylintest to ibckylintesa "10.0000 EOSPG"
````
Cleos ${kylin-api} push action ibctoken2bos transfer '["ibckylintest","ibckylintesa","10.0000 EOSPG" "transfer"]' -p ibckylintest
Cleos ${kylin-api} get currency balance ibctoken2bos ibckylintest #reduce 10 EOSPG
Cleos ${kylin-api} get currency balance ibctoken2bos ibckylintesa #增10 EOSPG
````

*Note: Due to the cross-chain transfer, there will be a delay in the arrival time*