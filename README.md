# Intro
DCRM SDK is a distributed key generation and distributed signature module that forms the cornerstone of decentralized value exchange.  This technology was developed for over a year, with the feedback of 4 leading cryptographers: Rosario Gennaro, Steven Goldfeder, Pascal Paillier, Louis Goubin. 

When used in context of blockchain, this module can serve as a non-custodial solution, a keyless wallet, a component to an interoperable solution, and more. Please read the [Wiki](https://github.com/fsn-dev/dcrm-sdk/wiki) for more information.

This SDK allows you to connect to DCRM's network directly in either: 
1. a 2+1 configuration where you form a private group with 2 fusion nodes and your own node. 
2. a local configuration where you can set any ownership of nodes in your group. 

This library contains 2 functions:
1. Distributed key generation which returns the public key (dcrm_genPubkey)
2. Distributed signing of transactions (dcrm_sign)

*Note: dcrm-sdk is considered beta software. We make no warranties or guarantees of its security or stability.*

# Prerequisites
1. Linux terminal
2. Golang

# Setting Up
## Clone The Repository
To get started, launch your terminal and download the latest version of the SDK.
```
mkdir -p $GOPATH/src/github.com/fsn-dev

cd $GOPATH/src/github.com/fsn-dev

git clone https://github.com/fsn-dev/dcrm-sdk.git
```
## Switch to Directory
```
cd dcrm-sdk
```
## Build
Next compile the code.  Make sure you are in /dcrm-sdk directory.
```
make
```
## Run
Open access to the APIs by running the compiled code.  Again, make sure you are in the /dcrm-sdk directory. 
```
./bin/cmd/gdcrm
```
The `gdcrm` will provide two RPC APIs: 
1. `dcrm_genPubkey` 
2. `dcrm_sign`

The default RPC port is port 5559.

# JSON RPC API

#### dcrm_genPubkey

This API generates a public key in distributed fashion by the P2P group.

##### Parameters

None

##### Return

`error` - Error info.

`pubkey` - DCRM public key generated by the P2P group.

##### Example
```
// Request

curl -X POST -H "Content-Type":application/json --data '{"jsonrpc":"2.0","method":"dcrm_genPubkey","params":[],"id":67}' http://127.0.0.1:5559

// Result

{
"pubkey":"049ac626ee0f0f79a49d6ed37f14ff2ad4e4f45fddf6e5293bcaa6a607e5392b49dde27a8f0602e23bc5fa0b847bd28d46e2f2d1d0d8cf59514785e4276b28de9d"
}
```
#### dcrm_sign

This API generates a signature in distributed fashion by the P2P group.

##### Parameters

1. `DATA`,pubkey - the pubkey from `dcrm_genPubkey` request.
2. `String`,|HexNumber|TAG - the hash needing signature. The format is the original 32-bit hexadecimal string appended to the characters "0x".  
    For example: 0x19b6236d2e7eb3e925d0c6e8850502c1f04822eb9aa67cb92e5004f7017e5e41.

##### Return

`error` - Error info.

`rsv` - The signature string generated in distributed fashion by the P2P group.

##### Example

```
// Request

curl -X POST -H "Content-Type":application/json --data '{"jsonrpc":"2.0","method":"dcrm_sign","params":["049ac626ee0f0f79a49d6ed37f14ff2ad4e4f45fddf6e5293bcaa6a607e5392b49dde27a8f0602e23bc5fa0b847bd28d46e2f2d1d0d8cf59514785e4276b28de9d","0x19b6236d2e7eb3e925d0c6e8850502c1f04822eb9aa67cb92e5004f7017e5e41"],"id":67}' http://127.0.0.1:5559

// Result

{
"rsv":"FFBB398B95ED2ED308B0FE87BC254FFC2C9957742EA05C18A1411C672B74FBDF6FBD6F4915799F2B4186192581D4506039ADEB79C8EB954E779901FDB9575C8301"
}
```
## Run Local
The SDK can also be configured to run locally, where the user can control ownership of all nodes in the P2P group.  

#### Run bootnode
```
./bin/cmd/bootnode --genkey ./bootnode.key

./bin/cmd/bootnode --nodekey ./bootnode.key --addr :5550 --group 0

will print bootnode which use for run node with args --bootnodes
bootnode key such as enode://16ab118525ec559dde2640b513676b8df7368aac3a80cc5c9d9e8b9c71781c09103fe3e8b5dd17bf245f0c71b891ec4848b142852763ab2146a1e288df15da40@[::]:5550
```
##### Run nodes (3 nodes at least)

The Default setup parameters are: 
1) nodekey: node.key
2) rpcport: 5559
3) port: 5551
4) bootnodes: "enode://200cb94957955bfa331ce14b72325c39f3eaa6bcfa962308c967390e5722f6fda0f6080781fde6a025a6280fbf23f38ca454e51a6b75ddbc1f9d57593790545a@47.107.50.83:5550"

Note: If you want to reboot a node, please wait 1 minute after closing node before restarting the node.

Setup using default parameters:
```
./bin/cmd/gdcrm
./bin/cmd/gdcrm --genkey node.key
```
Setup with custom parameters:
```
./bin/cmd/gdcrm --rpcport 9012 --bootnodes "enode://16ab118525ec559dde2640b513676b8df7368aac3a80cc5c9d9e8b9c71781c09103fe3e8b5dd17bf245f0c71b891ec4848b142852763ab2146a1e288df15da40@192.168.1.104:12340" --port 12341 --nodekey "node1.key"
```
