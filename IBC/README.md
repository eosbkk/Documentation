
# IBC  Contents

* [IBC Design Principles](https://github.com/boscore/Documentation/tree/master/IBC/EOSIO_IBC_Priciple_and_Design.md)
* [Deployment Document](https://github.com/boscore/Documentation/tree/master/IBC/Deployment/README.md)
* [Token Registration and Management Document](https://github.com/boscore/Documentation/tree/master/IBC/Token_Registration_and_Management.md)
* [TestNet Beginner's Guide](https://github.com/boscore/Documentation/tree/master/IBC/BeginnerGuide/README.md)
* [Wallet Access document](https://github.com/boscore/Documentation/tree/master/IBC/WalletAccess/README.md)
* [Exchange Access Document](https://github.com/boscore/Documentation/tree/master/IBC/WalletAccess/README.md)

# 中文版

* [《IBC设计原理》](https://github.com/boscore/Documentation/tree/master/IBC/EOSIO_IBC_Priciple_and_Design_zh.md)
* [《部署文档》](https://github.com/boscore/Documentation/tree/master/IBC/Deployment/README_CN.md)
* [《Token注册及管理文档》](https://github.com/boscore/Documentation/tree/master/IBC/Token_Registration_and_Management.md)
* [《测试网使用新手指南》](https://github.com/boscore/Documentation/tree/master/IBC/BeginnerGuide/README_CN.md)
* [《钱包接入文档》](https://github.com/boscore/Documentation/tree/master/IBC/WalletAccess/README_CN.md)
* [《交易所接入文档》](https://github.com/boscore/Documentation/tree/master/IBC/WalletAccess/README_CN.md)


# IBC User Guide

### 1. Preface
On the two blockchains between which realized inter-blockchain communication, 
any token conforming to the `eosio.token specification` can register and use the IBC channel for inter-blockchain transfer
, please refer to [Token Registration and Management](Token_Registration_and_Management.md) for more information.
This article describes the IBC user interface and given command line examples.


### 2. "transfer" action 
function definition:
``` 
[[eosio::action]]
void transfer( name from, name to, asset quantity, string memo );
```

*1. "to" account*
The "to" account must be the account which deployed ibc.token contract. In BOS-EOS IBC system, the "to" account on 
both EOS mainnet and BOS mainnet are **bosibc.io**.  
*Note: StartEOS contributed the EOS mainnet account `bosibc.io`; as the encouragement for the community contributions, 
the short-name `io` in BOS mainnet will be delivered to StartEOS.*

*2. "quantity"*
The token must be registered, and the quantity amount must satisfies this token's quotas, see [Token Quotas](#4-token-quotas).

*3. "memo" format*
memo format for ibc transaction is very important, because you should provide acceptance accounts on peer chain in memo string. format is:

**{account_name}@{chain_name} {user-defined string}**

{user-defined string} is optinal  
{chain_name} is defined by "peerchain_name" in ibc.token's "globals" table.  

examples:  
'bosaccount11@bos happy new year 2019'  
'eosaccount11@eos'  


note: if you want to transfer token to "to" account itself, not want a ibc transaction, the memo string must star
with "local", otherwise the transaction will fail. source code refer `token::transfer_notify()` and ` token::transfer()`
in ibc.token contract.


### 3. Command Line Examples
Transfer 100 EOS from EOS mainnet account `eosaccount` to BOS mainnet `bosaccount`
```bash
$cleos -u <eos-mainnet-api> transfer eosaccount bosibc.io "100.0000 EOS" "bosaccount@bos hello!"
```

Withdraw 100 EOS from BOS mainnet account `bosaccount` to EOS mainnet `eosaccount2`
```bash
$cleos -u <bos-mainnet-api> transfer -c bosibc.io bosaccount bosibc.io "100.0000 EOS" "eosaccount2@eos hi!"
``` 

Transfer 100 BOS from BOS mainnet account `bosaccount` to EOS mainnet `eosaccount`
```bash
$cleos -u <bos-mainnet-api> transfer bosaccount bosibc.io "100.0000 BOS" "eosaccount@eos hello!"
```

Withdraw 100 BOS from EOS mainnet account `eosaccount` to BOS mainnet `bosaccount2`
```bash
$cleos -u <eos-mainnet-api> transfer -c bosibc.io eosaccount bosibc.io "100.0000 BOS" "bosaccount2@bos hi!"
``` 

After send transfer action, and waiting for **4 to 5 minutes** (in the case of BPs schedule replacement, it may need up to 8 minutes), 
you can go to the peer chains to check if you have received the token.
It takes so long to wait because that it has to wait for the transaction to enter LIB then start IBC operations,
for more IBC theory please refer to [EOSIO IBC Priciple and Design](EOSIO_IBC_Priciple_and_Design.md).

So users can transfer assets across the chains by using any existing mobile app eosio wallets, 
the existing wallets only need to support the ibc.token contract, because the transfer action interface definition of ibc.token 
contract is exactly the same as that of eosio.token contract


### 4. Token Quotas
All token quotas are defined in ibc.token contracts, take bosibc.io as an example of ibc.token contract, 
please refer to [Token Registration and Management](Token_Registration_and_Management.md) for detailed explanation.
you can get them by following command:
``` 
$cleos -u <eos-mainnet-api> get table bosibc.io bosibc.io accepts
$cleos -u <eos-mainnet-api> get table bosibc.io bosibc.io stats
$cleos -u <bos-mainnet-api> get table bosibc.io bosibc.io accepts
$cleos -u <bos-mainnet-api> get table bosibc.io bosibc.io stats
```

**Example Registered Tokens and Their Quotas**  
The following values are for reference only, possibly inconsistent with settings in ibc.token contracts, 
please query the contract with above commands for real-time quota.

***EOS** of EOS mainnet*

| Item | Value |
|----------|-------------|
| original token contract          | eosio.token |
| original token symbol            | EOS |
| peg token symbol                 | EOS |
| maximum acceptance               | 10000000000.0000 EOS |
| minimum once forward transfer    | **0.2000 EOS** |
| maximum once forward transfer    | 1000000.0000 EOS |
| maximum daily forward transfers  | 10000000.0000 EOS |
| minimum once reverse withdrawal  | **0.2000 EOS** |
| maximum once reverse withdrawal  | **10000.0000 EOS** |
| maximum daily reverse withdrawal | 1000000.0000 EOS |
| maximum transfers per minute     | 100 |
| maximum withdrawals per minute   | 100 |
| forward transfers success fee    | 0 EOS |
| reverse withdrawal success fee   | **0.1000 EOS** |
| forward transfers failed fee     | fixed 0.0500 EOS |
| reverse withdrawal failed fee    | fixed 0.0500 EOS |
| project name                     | - |
| project official website         | - |


***BOS** of BOS mainnet*

| Item | Value |
|----------|-------------|
| original token contract          | eosio.token |
| original token symbol            | BOS |
| peg token symbol                 | BOS |
| maximum acceptance               | 10000000000.0000 BOS |
| minimum once forward transfer    | **0.2000 BOS** |
| maximum once forward transfer    | 1000000.0000 BOS |
| maximum daily forward transfers  | 10000000.0000 BOS |
| minimum once reverse withdrawal  | **0.2000 BOS** |
| maximum once reverse withdrawal  | **100000.0000 BOS** |
| maximum daily reverse withdrawal | 1000000.0000 BOS |
| maximum transfers per minute     | 100 |
| maximum withdrawals per minute   | 100 |
| forward transfers success fee    | 0 BOS |
| reverse withdrawal success fee   | **0.1000 BOS** |
| forward transfers failed fee     | fixed 0.0500 BOS |
| reverse withdrawal failed fee    | fixed 0.0500 BOS |
| project name                     | boscore |
| project official website         | boscore.io |


***TPT** of EOS mainnet*

| Item | Value |
|----------|-------------|
| original token contract          | eosiotptoken |
| original token symbol            | TPT |
| peg token symbol                 | TPT |
| maximum acceptance               | 5900000000.0000 TPT |
| minimum once forward transfer    | **100.0000 TPT** |
| maximum once forward transfer    | 10000000.0000 TPT |
| maximum daily forward transfers  | 100000000.0000 TPT |
| minimum once reverse withdrawal  | **100.0000 TPT** |
| maximum once reverse withdrawal  | **10000000.0000 TPT** |
| maximum daily reverse withdrawal | 100000000.0000 TPT |
| maximum transfers per minute     | 50 |
| maximum withdrawals per minute   | 50 |
| forward transfers success fee    | 0 TPT |
| reverse withdrawal success fee   | **60.0000 TPT** |
| forward transfers failed fee     | fixed 30.0000 TPT |
| reverse withdrawal failed fee    | fixed 30.0000 TPT |
| project name                     | TokenPocket |
| project official website         | tokenpocket.pro |
>>>>>>> dc41f14a56df04bf952e9d34ffa357ec45401549
