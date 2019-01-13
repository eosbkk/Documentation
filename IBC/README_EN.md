# IBC User Guide
## Introduction

When the token information on one chain is registered to the ibc.token contract of its own chain and the other chain, the ibc system begins to accept the cross-chain transaction for this token. The ibc system supports cross-chain requirements for any number of tokens on both ends of the chain.

The ibc system has only one ibc.token contract on a chain, and multiple tokens mapped on the original chain are managed in this contract.

Need for token contracts that require cross-chain:
- First: its transfer interface must be identical to the transfer interface definition of the "eosio.token" contract.
- Second: its transfer function must contain require_recipient( to ); statement

After a token is registered
- The eosio user invokes the transfer interface of the token and provides the appropriate memo information to complete the transfer of the asset from the original chain to the token mapping chain.
- The eosio user invokes the ibc.token contract transfer interface and provides the appropriate memo information to complete the operation of mapping the assets back to the original chain.

## Cross-chain contract account

In order to ensure a unified user experience, both the EOS main network and the BOS main network will be deployed using the account `bosibc.io`. The user can specify the chain in `memo`.

*Note: Thanks to the EOS main network account contributed by the StartEOS team `bosibc.io`*


## Memo Schema

Transfer Memo format:
```
    ACCOUNTNAME@CHAINNAME TRANSFER_MESSAGE
    Account name@chain name Transfer note
```

Example:

- Transfer 100 EOS from the EOS main network account `eosuser1` to the BOS main network `bosuser2`
```
    Cleos transfer eosuser1 bosibc.io "100.0000 EOS" "bosuser2@bos This is a cross-chain transfer from EOS to BOS"
```

- Transfer 100 EOS from BOS main network account `bosuser2` to EOS main network `eosuser1`
```
    Cleos transfer bosuser2 bosibc.io "100.0000 EOS" "eosuser1@eos This is a cross-chain transfer from BOS to EOS"
```

*Description: Due to the cross-chain transfer, there will be a delay in the arrival time. It is safe to consider, waiting for both ends of the transaction to enter LIB, the time should be about 6 minutes*


Therefore, the user can complete the cross-chain asset using the existing mobile phone app wallet, but the existing wallet needs to support the ibc.token contract. The support is also very simple, because the transfer interface definition of the ibc.token contract and the transfer definition of eosio.token. The exact same, mobile app wallet can also provide a proprietary interface to enhance the user experience.
