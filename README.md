## Introduction

quai-nginx is the standard reverse proxy configuration for the go-quai client. It provides definitions for **port to shard** and **path to shard** mappings for websockets and http.

## Background Knowledge

Quai Network is a sharded layer-1 protocol where shards operate as an independent blockchains that periodically creates links to other shards in order to trustlessly transfer state. Since shards operate independently, all data requested from or submitted to the network must be routed to the correct shard.

`quai-nginx` provides a standard routing protocol to request data from the correct shard. The current supported shards are as follows:

**Prime Shard**

- prime

**Region Shards**

- cyprus
- paxos
- hydra

**Zone Shards**

- cyprus1
- cyprus2
- cyprus3
- paxos1
- paxos2
- paxos3
- hydra1
- hydra2
- hydra3

## Client Specifications

Each node broadcasts data for specific shards using a protocol defined port mapping:

| Shard Name | Http Port | Websocket Port |
| ---------- | --------- | -------------- |
| prime      | 9001      | 8001           |
| cyprus     | 9002      | 8002           |
| paxos      | 9003      | 8003           |
| hydra      | 9004      | 8004           |
| cyprus1    | 9200      | 8200           |
| cyprus2    | 9201      | 8201           |
| cyprus3    | 9202      | 8202           |
| paxos1     | 9220      | 8220           |
| paxos2     | 9221      | 8221           |
| paxos3     | 9222      | 8222           |
| hydra1     | 9240      | 8240           |
| hydra2     | 9241      | 8241           |
| hydra3     | 9242      | 8242           |

## Proxy Specification

The nginx reverse proxy sets a standard human readable path for each shard that maps to the corresponding http and websocket ports. The path for each shard is mapped to the shard name as a subpath of the given URL:

| Shard Name | Path      |
| ---------- | --------- |
| prime      | /prime   |
| cyprus     | /cyprus  |
| paxos      | /paxos   |
| hydra      | /hydra   |
| cyprus1    | /cyprus1 |
| cyprus2    | /cyprus2 |
| cyprus3    | /cyprus3 |
| paxos1     | /paxos1  |
| paxos2     | /paxos2  |
| paxos3     | /paxos3  |
| hydra1     | /hydra1  |
| hydra2     | /hydra2  |
| hydra3     | /hydra3  |

For example, requesting the path `https://RPCURL/cyprus1/` will route the request to the cyprus1 http port (9200). Requesting the path `wss://RPCURL/cyprus1/` will route the request to the cyprus1 websocket port (8200).

## SDK Implications

`quai-nginx` is designed to create a standard interface for [quais-sdk](https://docs.qu.ai/sdk/introduction) providers to utilize. On initialization, Quais.js providers send a request to the given RPC endpoint to determine whether the provider supports the shard mapping defined in the `quai-nginx` specification.

This allows developers to configure a single provider with access to every shard in the network using a just one RPC URL.
