
**Welcome to Beam Testnet 2**

Node binaries can be downloaded from the Beam website (http://beam.mw/downloads) or from Github (https://github.com/beam-mw/beam-builds/tree/master/testnet/release !!!!!!!
http://builds.beam-mw.com/testnet2/2018.11.06/Release)

You can run the node with command line options, or modify the attached config file. Once you start the node it will create a node.db file in the same folder. This file will store internal state of the node.

**Reporting issues**

In case you encounter any problems, please report them via mail to testnet@beam-mw.com or open a github ticket at https://github.com/BeamMW/beam/issues

Attach archive with logs, configuration file and command line parameters to allow effective investigation of the issues.

**Running a node**

Node parameters can be either passed through command line or written in the beam-node.cfg file. 

Example:

*./beam-node --peer 104.248.159.154:8100 --wallet_phrases memory;cancel;brave;play;power;tomorrow;drama;paddle;city;prize;edit;cube; --mining_threads 1 --file_log_level debug*

The --peer parameter specifies a comma separated list of peers node will initially connect to. After the connection is etablished, node will get updated list of peers from the nodes, along with peer ratings and from that moment will manage the connections on its own. 

--wallet_seed - is a secret key for the wallet that will connect to this node to collect mining rewards (if the node is mining) 

--wallet_phrases - phrases to generate secret key according to BIP-39. <wallet_seed> option will be ignored

--mining_treads - specifies the number of CPU cores utilized for mining. If set to 0, node acts as validating node only.

--file_log_level - allows you to see that is going on behind the scenes.

Upon first launch, the node will download current blockchain history in batch mode as one large macroblock. After the initial sync is complete, the node will continue to sync blocks and individual transactions from the current blockchain Tip and onwards. This can be seen in the log entry:

*My Tip: 24704-f2ab414ba6430d85*

Here is the full list of options supported by the node

General options:
  -h [ --help ]                         list of all options
  -p [ --port ] arg (=10000)            port to start the server on
  --wallet_seed arg                     secret key generation seed
  --wallet_phrases arg                  phrases to generate secret key
                                        according to BIP-39. <wallet_seed>
                                        option will be ignored
  --log_level arg                       log level [info|debug|verbose]
  --file_log_level arg                  file log level [info|debug|verbose]
  -v [ --version ]                      return project version
  --git_commit_hash                     return commit hash


Node options:
  --storage arg (=node.db)              node storage path
  --history_dir arg (=./)               directory for compressed history
  --temp_dir arg (=/tmp/)               temp directory for compressed history,
                                        must be on the same volume
  --mining_threads arg (=0)             number of mining threads(there is no
                                        mining if 0)
  --verification_threads arg (=-1)      number of threads for cryptographic
                                        verifications (0 = single thread, -1 =
                                        auto)
  --miner_id arg (=0)                   seed for miner nonce generation
  --peer arg                            nodes to connect to
  --import arg (=0)                     Specify the blockchain height to
                                        import. The compressed history is
                                        asumed to be downloaded the the
                                        specified directory

Rules configuration:
  --CoinbaseEmission arg (=80000000)    coinbase emission in a single block
  --MaturityCoinbase arg (=60)          num of blocks before coinbase UTXO can
                                        be spent
  --MaturityStd arg (=0)                num of blocks before non-coinbase UTXO
                                        can be spent
  --MaxBodySize arg (=1048576)          Max block body size [bytes]
  --DesiredRate_s arg (=60)             Desired rate of generated blocks
                                        [seconds]
  --DifficultyReviewCycle arg (=1440)   num of blocks after which the mining
                                        difficulty can be adjusted
  --MaxDifficultyChange arg (=2)        Max difficulty change after each cycle
                                        (each step is roughly x2 complexity)
  --TimestampAheadThreshold_s arg (=7200)
                                        Block timestamp tolerance [seconds]
  --WindowForMedian arg (=25)           How many blocks are considered in
                                        calculating the timestamp median
  --AllowPublicUtxos arg (=0)           set to allow regular (non-coinbase)
                                        UTXO to have non-confidential signature
  --FakePoW arg (=0)                    Don't verify PoW. Mining is simulated
                                        by the timer. For tests only


**Running a command line wallet**

After extracting the wallet binary to a folder, first we need to initialize the wallet by running:

*./beam-wallet --command init*

You will be prompted to provide wallet password and then seed (or wallet_phrases). If you are using a miner node, the seed (phrases) should be the same as was set in the --wallet_seed (or wallet_phrases) flag of the node (if you use wallet_phrases - should be the same)


**Printing wallet information**

To get the information about the status of the wallet, run:

*./beam-wallet --command info -n 127.0.0.1:10000*


**Receiving Beams**

To receive beams we need to start a wallet in a listening mode by running:

*./beam-wallet --command listen -n 127.0.0.1:10000;*

After entering the password, wallet will print out the line similar to:

*WalletID 4a0e54b24d5fdf06891a8eaa57b4b3ac16731e932a64da8ec768083495d624f1 subscribes to BBS channel 9*

This show the SBBS address the wallet is listening on. This address can be copied and sent to Sender.

If you want to create new BBS address use next command:
*./beam-wallet --command=new_addr --listen -n 127.0.0.1:10000;*

**Sending Beams**

*./beam-wallet --command=send -n 127.0.0.1:10000 -r 77de6bd3de40bc58ab7e4fb68d5e0596fd1e72f3c4fb3eb3d106082d89264909 -a 11.3 -f 0.2*

To send Beams, use 'send' command with the following parameters:
-r <SBBS address of the receiver node> 
-a <amount of beams to send>
-f <transaction fee>


**Full list of wallet options**

General options:
  -h [ --help ]                         list of all options
  -p [ --port ] arg (=10000)            port to start the server on
  --wallet_seed arg                     secret key generation seed
  --wallet_phrases arg                  phrases to generate secret key
                                        according to BIP-39. <wallet_seed>
                                        option will be ignored
  --log_level arg                       log level [info|debug|verbose]
  --file_log_level arg                  file log level [info|debug|verbose]
  -v [ --version ]                      return project version
  --git_commit_hash                     return commit hash

Wallet options:
  --pass arg                            password for the wallet
  -a [ --amount ] arg                   amount to send (in Beams, 1 Beam =
                                        1000000 chattle)
  -f [ --fee ] arg (=0)                 fee (in Beams, 1 Beam = 1000000
                                        chattle)
  -r [ --receiver_addr ] arg            address of receiver
  -n [ --node_addr ] arg                address of node
  --treasury_path arg (=treasury.mw)    Block to create/append treasury to
  --wallet_path arg (=wallet.db)        path to wallet file
  --bbs_keystore_path arg (=bbs_keys.db)
                                        path to file with bbs keys
  --tx_history                          print transacrions' history in info
                                        command
  --tr_Count arg (=30)                  treasury UTXO count
  --tr_HeightStep arg (=1440)           treasury UTXO height lock step
  --tr_BeamsPerUtxo arg (=10)           treasury value of each UTXO (in Beams)
  --command arg                         command to execute [send|receive|listen
                                        |init|info|treasury]

Rules configuration:
  --CoinbaseEmission arg (=80000000)    coinbase emission in a single block
  --MaturityCoinbase arg (=60)          num of blocks before coinbase UTXO can
                                        be spent
  --MaturityStd arg (=0)                num of blocks before non-coinbase UTXO
                                        can be spent
  --MaxBodySize arg (=1048576)          Max block body size [bytes]
  --DesiredRate_s arg (=60)             Desired rate of generated blocks
                                        [seconds]
  --DifficultyReviewCycle arg (=1440)   num of blocks after which the mining
                                        difficulty can be adjusted
  --MaxDifficultyChange arg (=2)        Max difficulty change after each cycle
                                        (each step is roughly x2 complexity)
  --TimestampAheadThreshold_s arg (=7200)
                                        Block timestamp tolerance [seconds]
  --WindowForMedian arg (=25)           How many blocks are considered in
                                        calculating the timestamp median
  --AllowPublicUtxos arg (=0)           set to allow regular (non-coinbase)
                                        UTXO to have non-confidential signature
  --FakePoW arg (=0)                    Don't verify PoW. Mining is simulated
                                        by the timer. For tests only

**List of testnet nodes**

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