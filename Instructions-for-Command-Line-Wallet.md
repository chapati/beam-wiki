# Running a command line wallet

* After extracting the wallet binary to a folder, we need to initialize the wallet by executing the following command: 
``` sh
./beam-wallet --command init
```
* You will be prompted to provide wallet password and then the secret phrase, which would be used to initialize the master secret. Make sure to write down the secret phrase, to be able to restore wallet contents in case of emergency.


# Printing wallet information

* To get the information about the current status of the wallet, execute:
``` sh
./beam-wallet --command info -n 127.0.0.1:10000
```


# Receiving Beams

* To receive beams start the wallet in a listening mode by running: 
``` sh
./beam-wallet --command listen -n 127.0.0.1:10000
```
* After entering the password, the wallet will print out the line similar to: `WalletID 4a0e54b24d5fdf06891a8eaa57b4b3ac16731e932a64da8ec768083495d624f1 subscribes to BBS channel 9`
* This shows the SBBS address the wallet is listening on. This address can be copied and sent to Sender.
* If you want to create new SBBS address use the following command: 
``` sh
./beam-wallet --command=new_addr --listen -n 127.0.0.1:10000
```

# Exporting keys for standalone Node(s)

The Node(s) that belongs to the wallet should be given appropriate keys for the following purpose:
* Node-Wallet authentication
* Standalone mining (when the wallet is offline)
* Monitoring wallet activity, supporting recovery and etc.

It's possible to export the master wallet key, as well as a _child_ key, which is derived from the master key (but not vice versa!).
In addition every key can be exported as a full key, or as a _viewer_ key, which is suitable for Node authentication and monitoring activity, but not for generating and spending the UTXOs.

Keeping in mind that the wallet may own several nodes, we recommend providing each node a different child key for mining, so that in case it gets hacked - the attacker won't be able to steal what's been mined by other nodes. Anyway when the owner wallet connects the Node prefers to involve the wallet in mining, so that key won't be used either.
Obviously it's not recommended to export the master secret key ever.

In addition to mining, one or more viewer keys should be exported and provided to the nodes. To be able to communicate with the wallet, each Node must have its master viewer key. In addition to this, every node should have all the viewer child keys that are used for mining, to be able to detect/restore all the mined UTXOs.

* To export a child key
``` sh 
./beam-wallet --command=key_export --subkey=N
```
whereas `N` is the child index. Specifying `0` means the master key.

# Sending Beams

* To sending beams use the following command 
``` sh 
./beam-wallet --command=send -n 127.0.0.1:10000 -r 77de6bd3de40bc58ab7e4fb68d5e0596fd1e72f3c4fb3eb3d106082d89264909 -a 11.3 -f 0.2
```
* The send-related command line parameters of the wallet:

| Name | Description |
|------|-------------|
| `r` | SBBS address of the receiver node |
| `a` | amount of beams to send |
| `f` | transaction fee |


# The Full list of wallet command line options

## General options:
| Name | Description |
|------|-------------|
| `h` or `help` | list of all options |
| `p`  or `port arg (=10000)` | port to start the server on |
| `log_level arg` | log level `[info|debug|verbose]` |
| `file_log_level arg` | file log level `[info|debug|verbose]` |
| `v` or `version` | return project version |
| `git_commit_hash` | return commit hash |

## Wallet options:

| Name | Description |
|------|-------------|
| `seed_phrase arg` | phrase to initialize master secret, according to BIP-39 |
| `pass arg` | password for the wallet |
| `a` or `amount arg` | amount to send (in Beams, 1 Beam = 1000000 chattle) |
| `f` or `fee arg (=0)` | fee (in Beams, 1 Beam = 1000000 chattle) |
| `r` or `receiver_addr arg` | address of receiver |
| `n` or `node_addr arg` | address of node |
| `wallet_path arg (=wallet.db)` | path to wallet file |
| `bbs_keystore_path arg (=bbs_keys.db)` | path to file with bbs keys |
| `tx_history` | print transactions' history in info command |
| `command arg` | command to execute `[send|receive|listen|init|info]`

## Rules configuration:

| Name | Description |
|------|-------------|
| `CoinbaseEmission arg (=80000000)` | coinbase emission in a single block |
| `MaturityCoinbase arg (=60)`  | Number of blocks before coinbase UTXO can be spent |
| `MaturityStd arg (=0)` | Number of blocks before non-coinbase UTXO can be spent |
| `MaxBodySize arg (=1048576)` | Max block body size in `[bytes]` |
| `DesiredRate_s arg (=60)`| Desired rate of generated blocks in `[seconds]` |
| `DifficultyReviewCycle arg (=1440)` | Number of blocks after which the mining difficulty can be adjusted |
| `MaxDifficultyChange arg (=2)` | Max difficulty change after each cycle (each step is roughly x2 complexity) |
| `TimestampAheadThreshold_s arg (=7200)` | Block timestamp tolerance in `[seconds]` |
| `WindowForMedian arg (=25)` | How many blocks are considered in calculating the timestamp median |
| `AllowPublicUtxos arg (=0)` | Set to allow regular (non-coinbase) UTXO to have non-confidential signature |
| `FakePoW arg (=0)` | Don't verify PoW. Mining is simulated by the timer. For tests only. |

# List of Тestnet nodes

The Тestnet nodes can be used in the following scenarios:
* When specifying peers for the beam-node
* Where a wallet connects to the specific node

```
139.59.174.80:8100
167.99.221.104:8100
159.65.201.244:8100
142.93.217.246:8100
142.93.221.22:8100
178.62.59.65:8100
139.59.169.77:8100
167.99.166.33:8100
159.65.119.216:8100
142.93.221.66:8100
206.189.10.149:8100
138.68.134.136:8100
159.65.119.226:8100
142.93.221.80:8100
104.248.159.97:8100
159.65.101.94:8100
68.183.103.130:8100
142.93.213.21:8100
104.248.159.154:8100
188.166.165.240:8100
159.65.104.75:8100
142.93.213.106:8100
```

# Known limitations
* CPUs without SSE3 instruction set are not supported
