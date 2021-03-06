
IBC System Deployment
-----

This article will detail how to build, deploy and use the IBC system, and take the deployment between `Kylin testnet`
and the `BOS testnet` as an example. If you want to deploy IBC system between EOSIO chains whose source code unmodified, 
such as between `EOS mainnet` and `Kylin or Jungle testnet`, you need not `ibc_plugin_bos`, 
only `ibc_plugin_eos` is needed; if you want to deploy between EOSIO chains whose underlying source code has been modified,
it's need to determine whether those chains can use the `ibc_contracts` and `ibc_plugin` according to the actual situation.

### Build
- Build contracts  
  Please refer to [ibc_contracts](https://github.com/boscore/ibc_contracts).
  
- Build ibc_plugin_eos and ibc_plugin_bos  
  Please refer to [ibc_plugin_eos](https://github.com/boscore/ibc_plugin_eos) and [ibc_plugin_bos](https://github.com/boscore/ibc_plugin_bos).
  
### Create Accounts
Three accounts need to be registered on each chain， one relay account for ibc_plugin, and two contract accounts 
for deploying IBC contracts, let's assume that the three accounts on both chains are:   
**ibc3relay333**: used by ibc_plugin, and need a certain amount of cpu and net.   
**ibc3chain333**: used to deploy ibc.chain contract, and need a certain amount of ram, such as 5Mb.  
**ibc3token333**: used to deploy ibc.token contract, and need a certain amount of ram, such as 10Mb.  
  
### Configure and Start Relay Nodes
Suppose that the IP address and port of the relay node of Kylin testnet are 192.168.1.101:5678, node of BOS testnet are
192.168.1.102:5678

Some key configurations and ibc-related configurations are listed below. 

- configure relay node of Kylin testnet  
``` 
max-transaction-time = 1000
contracts-console = true

# ------------ ibc related configures ------------ #
plugin = eosio::ibc::ibc_plugin

ibc-listen-endpoint = 0.0.0.0:5678
#ibc-peer-address = 192.168.1.102:5678 
ibc-max-nodes-per-host = 5

ibc-token-contract = ibc3token333
ibc-chain-contract = ibc3chain333
ibc-relay-name = ibc3relay333
ibc-relay-private-key = <the pulic key of ibc3relay333>=KEY:<the private key of ibc3relay333>

ibc-sidechain-id = <BOS testnet chain id>
ibc-peer-private-key = <ibc peer public key>=KEY:<ibc peer private key>

# ------------------- p2p peers -------------------#
p2p-peer-address = <Kylin testnet p2p endpoint>
```

- configure relay node of BOS testnet  
``` 
max-transaction-time = 1000
contracts-console = true

# ------------ ibc related configures ------------ #
plugin = eosio::ibc::ibc_plugin

ibc-listen-endpoint = 0.0.0.0:5678
ibc-peer-address = 192.168.1.101:5678 
ibc-max-nodes-per-host = 5

ibc-token-contract = ibc3token333
ibc-chain-contract = ibc3chain333
ibc-relay-name = ibc3relay333
ibc-relay-private-key = <the pulic key of ibc3relay333>=KEY:<the private key of ibc3relay333>

ibc-sidechain-id = <Kylin testnet chain id>
ibc-peer-private-key = <ibc peer public key>=KEY:<ibc peer private key>

# ------------------- p2p peers -------------------#
p2p-peer-address = <BOS testnet p2p endpoint>
```

- relay nodes's log  
When the relay nodes of two chains started, they try to connect with each other, 
and check the ibc contracts on their respective chains, and print the logs according to the contracts status.


### Deploy and Init IBC Contracts
- ibc.chain and ibc.token contracts on Kylin testnet  
```bash
cleos1='cleos -u <Kylin testnet http endpoint>'
contract_chain=ibc3token333
contract_token=ibc3chain333
contract_token_pubkey='<public key of contract_token>'

$cleos1 set contract ${contract_chain} <contract_chain_folder> -x 1000 -p ${contract_chain}
$cleos1 set contract ${contract_token} <contract_token_folder> -x 1000 -p ${contract_token}

$cleos1 push action ${contract_chain} setglobal '[{"lib_depth":85}]' -p ${contract_chain}
$cleos1 push action ${contract_chain} relay '["add","ibc3relay333"]' -p ${contract_chain}

$cleos1 set account permission ${contract_token} active '{"threshold": 1, "keys":[{"key":"'${contract_token_pubkey}'", "weight":1}], "accounts":[ {"permission":{"actor":"'${contract_token}'","permission":"eosio.code"},"weight":1}], "waits":[] }' owner -p $ {contract_token}
$cleos1 push action ${contract_token} setglobal '["ibc3chain333","bostest","ibc3token333",5000,1000,10,true]' -p ${contract_token}
```

- ibc.chain and ibc.token contracts on BOS testnet  
```bash
cleos2='cleos -u <BOS testnet http endpoint>'
contract_chain=ibc3token333
contract_token=ibc3chain333
contract_token_pubkey='<public key of contract_token>'

$cleos2 set contract ${contract_chain} <contract_chain_folder> -x 1000 -p ${contract_chain}
$cleos2 set contract ${contract_token} <contract_token_folder> -x 1000 -p ${contract_token}

$cleos2 push action ${contract_chain} setglobal '[{"lib_depth":85}]' -p ${contract_chain}
$cleos2 push action ${contract_chain} relay '["add","ibc3relay333"]' -p ${contract_chain}

$cleos2 set account permission ${contract_token} active '{"threshold": 1, "keys":[{"key":"'${contract_token_pubkey}'", "weight":1}], "accounts":[ {"permission":{"actor":"'${contract_token}'","permission":"eosio.code"},"weight":1}], "waits":[] }' owner -p $ {contract_token}
$cleos2 push action ${contract_token} setglobal '["ibc3chain333","kylin","ibc3token333",5000,1000,10,true]' -p ${contract_token}
```

After initializing the two contracts, the relay nodes of two chains will start synchronizing the first block header of 
peer chain as the genesis block header of ibc.chain contract, then synchronize a batch of block headers.


### Register Token
Please refer to detailed content [Token_Registration_and_Management](Token_Registration_and_Management.md)

- register `EOS Token` of Kylin testnet in `ibc.token of Kylin testenet` and in `ibc.token of BOS testenet`   
```bash
eos_admin=ibc3admin333
$cleos1 push action ${contract_token} regacpttoken \
    '["eosio.token","1000000000.0000 EOS","'${eos_admin}'","0.2000 EOS","1000.0000 EOS",
    "100000.0000 EOS",100,"boscore","https://www.boscore.io","fixed","0.1000 EOS",0.0,"fixed","0.0500 EOS",0.0,true,"4,EOS"]' -p ${contract_token}

$cleos2 push action ${contract_token} regpegtoken \
    '["1000000000.0000 EOS","0.2000 EOS","1000.0000 EOS",
    "100000.0000 EOS",100,"'${eos_admin}'","eosio.token","4,EOS","fixed","0.0500 EOS",0.0,true]' -p ${contract_token}
```

- register `BOS Token` of BOS testnet in `ibc.token of BOS testenet` and in `ibc.token of Kylin testenet`   
```bash
bos_admin=ibc3admin333
$cleos2 push action ${contract_token} regacpttoken \
    '["eosio.token","1000000000.0000 BOS","'${bos_admin}'","0.2000 BOS","1000.0000 BOS",
    "100000.0000 BOS",100,"boscore","https://www.boscore.io","fixed","0.1000 BOS",0.0,"fixed","0.0500 BOS",0.0,true,"4,BOS"]' -p ${contract_token}

$cleos1 push action ${contract_token} regpegtoken \
    '["1000000000.0000 BOS","0.2000 BOS","1000.0000 BOS",
    "100000.0000 BOS",100,"'${bos_admin}'","eosio.token","4,BOS","fixed","0.0500 BOS",0.0,true]' -p ${contract_token}
```

### Send IBC Transactions

- create some accounts on both chains  
```bash
eos_acnt1=eosaccount11  # account on Kylin testnet
eos_acnt2=eosaccount12  # account on Kylin testnet
bos_acnt1=bosaccount11  # account on BOS testnet
bos_acnt2=bosaccount12  # account on BOS testnet
```

- send EOS form Kylin testnet ${eos_acnt1} to BOS testnet ${bos_acnt1}  
```bash
$cleos1 transfer ${eos_acnt1} ${contract_token} "1.0000 EOS" ${bos_acnt1}"@bostest this is a ibc transaction" -p ${eos_acnt1}
```

- withdraw EOS(peg token) form BOS testnet ${bos_acnt1} to Kylin testnet ${eos_acnt2}  
```bash
$cleos2 push action ${contract_token} transfer '["'${bos_acnt1}'","'${contract_token}'","1.0000 EOS" "'${eos_acnt2}'@kylin"]' -p ${bos_acnt1}
```

- send BOS form BOS testnet ${bos_acnt1} to Kylin testnet ${eos_acnt1}  
```bash
$cleos2 transfer ${bos_acnt1} ${contract_token} "1.0000 BOS" ${eos_acnt1}"@kylin this is a ibc transaction" -p ${bos_acnt1}
```

- withdraw BOS(peg token) form Kylin testnet ${eos_acnt1} to BOS testnet ${bos_acnt2}  
```bash
$cleos1 push action ${contract_token} transfer '["'${eos_acnt1}'","'${contract_token}'","1.0000 BOS" "'${bos_acnt2}'@bostest"]' -p ${eos_acnt1}
```
