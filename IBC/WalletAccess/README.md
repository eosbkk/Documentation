# wallet access document

1. Wallet access basic interface
1. Get account balance
1. Obtain the account system token balance
1. Transfer tokens to the specified account
1. Extract tokens from the specified account
1. Get the most recent transaction list
1. Get transaction details based on txid

## Wallet access basic interface

### Get account balance
* Function: Get account balance
* Command line interface form:
```
Cleos get table relay token account account
```
* Parameters:
* Relay Token Contract Account ibctoken2bos
* Designated account

Command line example

```
$cleos2 get table ibctoken2bos $1 accounts

```
  
RPC API example

```
 Curl --request POST \
  --url http://127.0.0.1:8888/v1/chain/get_table_rows \
  --data '{"scope":"receiverbos1","code":"eosio.token","table":"accounts","json":"true"}'
```
### Get the account system token balance

* Function: Get account system token balance
* Command line interface form:

```
 Cleos get currency balance system token contract account designated account token symbol
```
* Parameters:
* System token contract account eosio.token
* Designated account
* Token symbol

Command line example

```
$cleos2 get currency balance eosio.token receiverbos1 "BOS"
```
  
RPC API example

```
Curl --request POST --url 'http://127.0.0.1:8888/v1/chain/get_currency_balance' -d '{"code":"eosio.token", "account":"receiverbos1","symbol" :"
BOS"}'
```

### Transferring tokens to a specified account
* Function: Transfer tokens to the specified account
* Command line interface form:

```
Cleos transfer designated account relay token account amount amount remarks -p permission
```
* Parameters:
* Designated account
* Relay Token Contract Account ibctoken2bos
* Amount
* Remarks
* Permissions

Command line example

```
$cleos1 transfer -f firstaccount ibctoken2bos "10.0000 EOS" "chengsong111@bos notes infomation" -p firstaccount
    
$cleos2 transfer -f firstaccount ibctoken2eos "10.0000 BOS" "chengsong111@eos notes infomation" -p firstaccount
```
### Extract tokens from the specified account

* Function: Send tokens to the specified account
* Command line interface form:
```
 Clearos push action relay token account transfer '["specified account", "relay token account", "amount", "remarks"]' -p permission
```
* Parameters:
* Designated account
* Relay Token Contract Account ibctoken2bos
* Amount
* Remarks


Command line example

```
$cleos1 push action -f ibctoken2bos transfer '["chengsong111","ibctoken2bos","10.0000 BOSPG" "receiverbos1@bos notes infomation"]' -p chengsong111

$cleos2 push action -f ibctoken2eos transfer '["chengsong111","ibctoken2eos","10.0000 EOSPG" "receivereos1@eos notes infomation"]' -p chengsong111
```
    
### Get the most recent list of deals

* Function: Get the most recent transaction list
* Command line interface form:
 
```
 Cleos get table relay token account relay token contract account origtrxs
```
* Parameters:
* Relay Token Contract Account ibctoken2bos



Command line example

```
 $cleos2 get table ibctoken2bos ibctoken2bos origtrxs
```
  
RPC API example
```
 Curl --request POST \
  --url http://127.0.0.1:8888/v1/chain/get_table_rows \
  --data '{"scope":"ibctoken2bos","code":"ibctoken2bos","table":"origtrxs","json":"true"}'
```

### Get transaction details based on txid
* Function: Get transaction details based on txid
* Command line interface form:
* cleos get transaction transaction ID
* Parameters:
* Transaction ID

Command line example

```
Cleos get transaction eb4b94b72718a369af09eb2e7885b3f494dd1d8a20278a6634611d5edd76b703
```
RPC API example

```
Curl --request POST \
  --url http://host/:port/v1/history/get_transaction
```