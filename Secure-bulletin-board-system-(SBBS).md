# Secure bulletin board system (SBBS)
## Overview
The main goal of BBS is to allow wallets to communicate with each other in a secure and asynchronous manner. Using BBS wallets allows individuals to exchange messages, even if one of individuals is offline. In general, BBS is a virtual board, where users can place messages, and each message is encrypted. For encryption the public key of the recipient is used. This implies that recipient’s public key is his address in terms of this system. Every participant who is interested in messages from this board, observes and tries to decrypt new messages with his private key, and he manages to do so only if the message has been addressed to him. It consists of server and client sides. The server is implemented as a part of the node. The client is a wallet.

## Server side
BBS is a part of our node, this is not very good in general, but a working example is good enough to start. This implementation implies that messages are sent to n=32 (could be changed) channels. The channel is calculated from the public key (first 5 bits). Client has to subscribe to specific channel, and server will retranslate all new messages to subscribers. Also, server receives and stores in data base all new messages and exchange these messages with other servers using p2p protocol. This logic  implemented in class Node::Peer. It represents node’s peer and handles node’s P2P message. BBS messages are a part of node p2p protocol.

#### BbsSubscribe
Message tp allow clent to subscribe/unsubscribe to notification from node about new messages.

* `BbsChannel m_Channel` – channel to listen.
* `Timestamp m_TimeFrom` – timestamp used to avoid sending of out of date messages.
* `bool m_On` – subscribe/unsubscribe flag.

When server receives this message from peer with `m_On == true` and `Node::Peer::m_Subscriptions` list doesn’t have it (the look up is made by channel), then node adds new instance of `Node::Bbs::Subscription` to this list and to Node:: `m_Bbs::m_Subscribed`. If node has messages for this channel sent after given timestamp, then it sends them as new `BbsMsg `to this subscriber. If `m_On == false` and given subscriber exists in `m_Subscriptions` list, node simply removes it.


#### BbsMsg
Message which is used to put encrypted message on to the board or to exchange between nodes.

* `BbsChannel m_Channel` – target channel.
* `Timestamp m_TimePosted` – timestamp, when wallet has posted message to bbs.
* `ByteBuffer m_Message` – the encrypted content.

When this message is received, server gets current timestamp. If `m_TimePosted` <= currentTime-24 hours, i.e. to old, or `m_TimePosted` > currentTime + 2 hour (someone generates messages from future), then node rejects this message. If it doesn’t have this message it stores it in database, sends `BbsHaveMsg`, to inform other peers about new bbs message, and sends `BbsMsg `to subscriberd interested in messages from `m_Channel`.

#### BbsHaveMsg
Message which is used by BBS server (node) to exchange information about message it has.
* `BbsMsgID m_Key` – the key of the message, it defines as a hash of the `m_Message` and the `m_Channel` in `BbsMsg`
In order to handle this message node checks if it already has this message, if it doesn’t it stores it into wait list `Node::m_Bbs::m_W` and sends `BbsGetMsg`.

#### BbsGetMsg
Peer server request for bbs message, occurs as a response to `BbsHaveMsg`.

* `BbsMsgID m_Key`
If node has bbs message with given key it sends it back as `BbsMsg`.

#### BbsPickChannel
Client asks server for recommended channel, this message is used together with.

#### BbsPickChannelRes
Server channel recommendation

* `BbsChannel m_Channel` - recommended channel. Once in an hour (`Node::m_Cfg::m_Timeout::m_BbsCleanupPeriod_ms`) node tries to find channel which is populated less than 100 listeners (`Node::m_Cfg::m_BbsIdealChannelPopulation`). Found value is stored into `m_RecommendedChannel` and it is used as a response to `BbsPickChannel`.


## Client side
In wallet BBS communication is placed in `WalletNetworkViaBbs` class. It allows to send message to BBS and it manages BBS keys and timestamps.
This class contains references to 

`IWallet& m_Wallet` - used for callbacks to wallet object.

`proto::FlyClient::INetwork& m_NodeNetwork` - used to communicate with node.

When wallet wants to create new address (from cli, ui ) or load already created addresses from database `AddOwnAddress()` is called. This method calls `IWalletDB::calcKey()` with key type Bbs to generate private key for BBS, the public key is created using proto::Sk2Pk(). Index, used for generation private key is stored in _wallet.db_. Then wallet choose channel. If client has not subscribed to chosen channel, the `BbsSubscribe` message is posted to node via `m_NodeNetwork`.
If wallet wants to send a message via Bbs it calls overridden method Send(). In this method given message is encrypted using `proto::BbsEncrypt()` and send it to `m_NodeNetwork`. If wallet received bbs message it updates timestamp for the channel and store them to database.

#### Usage of BBS to exchange message

There are two sides wallet **A** and wallet **B**.

##### **A** and **B**:

* picks/or generates a pair of keys public and private
* choose channel//asks BBS server (node) for suitable
* sends `BbsSubscribe` to chosen channel, if needed

##### Wallet **A**:

* chooses the address of target recipient (his public key)
* creates a message, it has to contain its public key as an address for answer
* encrypts this message using pub
* creates `BbsMsg`, fills it and sends to BBS server

##### Wallet **B**:

* receives `BbsMsg`
* updates timestamps for message channel
* tries to decrypt bbs message using known key for message channel
* if succeeded notifies wallet
