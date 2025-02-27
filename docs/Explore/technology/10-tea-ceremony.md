# Tea Ceremony algorithm

**Table of contents**

   - [Introduction](#introduction)
   - [Algorithm work scheme](#algorithm-work-scheme)

## Introduction

**Tea Ceremony** is an algorithm for secure generation of [`genesis.txt`](../../Maintain/build-and-start-a-node/02-tpNodeConfiguration.md#generation-of-genesistxt) and [node private and public keys](../../Maintain/build-and-start-a-node/04-private-keys-generation.md). It allows nodes to exchange public keys without needing to exchange private keys with each other. Thanks to the Tea Ceremony algorithm, the node private keys are stored only on the nodes they are generated for.

### Stages of Tea Ceremony

The Tea Ceremony has three steps:

1. Collection of public keys of all nodes in the chain.
2. Signing the settings patch.
3. Signing the block.

The Tea Ceremony client shows how many nodes have participated in each step.

## Algorithm work scheme

Tea Ceremony algorithm works as follows:

1. Chain administrators initiate Tea Ceremony by addressing to the API using `curl`. Here is an example of initialization:

   ```bash
   curl -d '{"chain_number":666,"nodes":3,"settings":[{"p":["current","chain","blocktime"],"v":2},{"p":["current","chain","minsig"],"v":2},{"p":["current","chain","allowempty"],"v":0},{"p":["current","chain","patchsigs"],"v":2},{"p":["current","allocblock","block"],"v":666},{"p":["current","allocblock","group"],"v":10},{"p":["current","allocblock","last"],"v":0},{"p":["current","endless",["!bin","800140029A000001"],"SK"],"v":true},{"p":["current","endless",["!bin","800140029A000001"],"TST"],"v":true},{"p":["current","freegas"],"v":2000000},{"p":["current","something_left"],"v":["!bin","01020304"]},{"p":["current","gas","SK"],"v":1000},{"p":["current","nosk"],"v":1}]}' https://tea.thepower.io/api/new_ceremony -H "content-type: application/json"
   ```
   where

   - parameters in `''` are `json`-formatted;
   - `https://tea.thepower.io/api/new_ceremony` — API address for Tea Ceremony initialization.

2. The request returns a Ceremony token.
3. The users start the Tea Ceremony client on each node in a chain. All node providers in the chain must start the Tea Ceremony client on their nodes to initiate generation of `genesis txt`. Otherwise, the node public keys will not be added into `genesis.txt` and will not be trusted.
4. The Tea Ceremony client [generates the private keys](../../Maintain/build-and-start-a-node/03-ssl-certs-for-node.md#private-key-generation) for nodes.
5. The Tea Ceremony client waits for `genesis.txt` to sign it.
6. The Tea Ceremony client sends the signed `genesis.txt` back to the node, where the client was started.
7. The Tea Ceremony client creates the node configuration files.
8. The Tea Ceremony waits for `genesis.txt` signed by all the nodes in the chain, and saves it.