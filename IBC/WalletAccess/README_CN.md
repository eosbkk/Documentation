

# 钱包接入文档

1. 钱包接入基本接口
	1. 	获取账户余额
	1. 	获取账户系统代币余额
	1. 	向指定的账户转账代币
	1. 	从指定的账户提取代币 
	1. 	获取最近的交易列表
	1. 	根据txid获取交易详细信息

## 钱包接入基本接口

### 获取账户余额
* 功能：获取账户余额
* 命令行接口形式：  
```
cleos get table 中继代币合约账户 指定账户 accounts
```
* 参数：
* 中继代币合约账户  ibctoken2bos
* 指定账户  

命令行示例

``` 
$cleos2 get table ibctoken2bos $1 accounts

```
  
RPC API 示例

```
 curl --request POST \
  --url http://127.0.0.1:8888/v1/chain/get_table_rows \
  --data '{"scope":"receiverbos1","code":"eosio.token","table":"accounts","json":"true"}'
```
### 获取账户系统代币余额

* 功能：获取账户系统代币余额
* 命令行接口形式：

```
 cleos get currency balance 系统代币合约账户 指定账户 代币符号
```
* 参数： 
* 系统代币合约账户  eosio.token 
* 指定账户 
* 代币符号

命令行示例

``` 
$cleos2 get currency balance eosio.token receiverbos1 "BOS"
```
  
RPC API 示例

``` 
curl --request POST --url 'http://127.0.0.1:8888/v1/chain/get_currency_balance' -d '{"code":"eosio.token", "account":"receiverbos1","symbol":"
BOS"}'
```

### 向指定的账户转账代币
* 功能：向指定的账户转账代币
* 命令行接口形式：

```  
cleos transfer 指定账户  中继代币合约账户  金额 备注 -p 权限
```
* 参数：
* 指定账户  
* 中继代币合约账户  ibctoken2bos
* 金额 
* 备注
* 权限

命令行示例

```
$cleos1 transfer -f firstaccount ibctoken2bos "10.0000 EOS" "chengsong111@bos notes infomation" -p firstaccount
    
$cleos2 transfer -f firstaccount ibctoken2eos "10.0000 BOS" "chengsong111@eos notes infomation" -p firstaccount
```
### 从指定的账户提取代币 

* 功能：向指定的账户发送代币
* 命令行接口形式：
``` 
 cleos push action   中继代币合约账户 transfer  '["指定账户"，"中继代币合约账户"，"金额"，"备注"]'  -p 权限
```
* 参数：
* 指定账户  
* 中继代币合约账户  ibctoken2bos
* 金额 
* 备注


命令行示例

```
$cleos1 push action -f ibctoken2bos transfer '["chengsong111","ibctoken2bos","10.0000 BOSPG" "receiverbos1@bos notes infomation"]' -p chengsong111

$cleos2 push action -f ibctoken2eos transfer '["chengsong111","ibctoken2eos","10.0000 EOSPG" "receivereos1@eos notes infomation"]' -p chengsong111
```
    
### 获取最近的交易列表

* 功能：获取最近的交易列表
* 命令行接口形式：
 
```
 cleos get table 中继代币合约账户 中继代币合约账户  origtrxs
```
* 参数：
* 中继代币合约账户  ibctoken2bos



命令行示例

```
 $cleos2 get table ibctoken2bos ibctoken2bos origtrxs
```
  
RPC API 示例
```
 curl --request POST \
  --url http://127.0.0.1:8888/v1/chain/get_table_rows \
  --data '{"scope":"ibctoken2bos","code":"ibctoken2bos","table":"origtrxs","json":"true"}'
```

### 根据txid获取交易详细信息
* 功能：根据txid获取交易详细信息
* 命令行接口形式：
* cleos get transaction   交易ID
* 参数：
* 交易ID

命令行示例

```
cleos get transaction eb4b94b72718a369af09eb2e7885b3f494dd1d8a20278a6634611d5edd76b703
```
RPC API 示例

```
curl --request POST \
  --url http://host/:port/v1/history/get_transaction
```




