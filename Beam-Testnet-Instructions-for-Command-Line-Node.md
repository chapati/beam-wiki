# Welcome to Beam Testnet 2

In case you encounter any problem, please email us at testnet@beam-mw.com or open a GitHub ticket at https://github.com/BeamMW/beam/issues

For effective investigation please attach the following items for every issue:
* Logs, compressed into a single archive
* Configuration file
* Command line parameters of the executed binary

# Running a node

* Node binaries for Windows, Mac and Linux can be downloaded from the Beam website (http://beam.mw/downloads) or GitHub (https://github.com/BeamMW/beam/releases/tag/testnet2).
* Node parameters can be either passed through command line or specified in the configuration file called `beam-node.cfg` and located in the same folder the node resides in.
* For every parameter, the value in the command line prevails over configuration file.
* The log files are located in the `logs` folder, which in turn resides in the node folder.
* Once started, the node will create a `node.db` file in the same folder it is located. This file will store an internal state of the node.
* Upon the first launch, the node will download current blockchain history in batch mode as a single large macroblock. After the initial sync is complete, the node will continue to sync blocks and individual transactions from the current blockchain Tip (height) and onwards. This can be seen in the log entry: `My Tip: 24704-f2ab414ba6430d85`. 

## Command line execution example:

`./beam-node --peer 104.248.159.154:8100 --wallet_phrases memory;cancel;brave;play;power;tomorrow;drama;paddle;city;prize;edit;cube; --mining_threads 1 --file_log_level debug`

### Node command line parameters explained:

* The `--peer` parameter specifies a comma-separated list of peers the node will initially connect to. After the connection is established, the node will get an updated list of peers from other nodes, along with peer ratings and from that moment the node will manage its connections on its own. 
* `--wallet_seed` - is a secret key for the wallet that will connect to this node to collect mining rewards (if the node is mining) 
* `--wallet_phrases` - phrases to generate secret key according to BIP-39. `wallet_seed` option will be ignored
* `--mining_treads` - specifies the number of CPU cores utilized for mining. If set to 0, node acts as a validating node only.
* `--file_log_level` - allows to raise the debug level when a deeper investigation is required

## Config file example:

`# port to start server on`

`port=10000`

`# secret key generation seed.`

`wallet_phrases=memory;cancel;brave;play;power;tomorrow;drama;paddle;city;prize;edit;cube;`

`# number of mining threads(there is no mining if 0)`

`mining_threads=1`

`# peers`

`peer=104.248.159.154:8100`

## The full list of options supported by the node

### General options:
* `-h [ --help ]`                         list of all options
* `-p [ --port ] arg (=10000)`            port to start the server on
* `--wallet_seed arg`                     secret key generation seed
* `--wallet_phrases arg`                  phrases to generate secret key according to BIP-39, `<wallet_seed>` option will be ignored
* `--log_level arg`                       log level [info|debug|verbose]
* `--file_log_level arg`                  file log level [info|debug|verbose]
* `-v [ --version ]`                      return project version
* `--git_commit_hash`                     return commit hash

### Node options:
* `--storage arg (=node.db)`              node storage path
* `--history_dir arg (=./)`               directory for compressed history
* `--temp_dir arg (=/tmp/)`               temp directory for compressed history, must be on the same volume
* `--mining_threads arg (=0)`             number of mining threads(there is no mining if 0)
* `--verification_threads arg (=-1)`      number of threads for cryptographic verifications (0 = single thread, -1 = auto)
* `--miner_id arg (=0)`                   seed for miner nonce generation
* `--peer arg`                            nodes to connect to
* `--import arg (=0)`                     specify the blockchain height to import, the compressed history is assumed to be downloaded to the specified directory

### Rules configuration:
* `--CoinbaseEmission arg (=80000000)`    coinbase emission in a single block
* `--MaturityCoinbase arg (=60)`          num of blocks before coinbase UTXO can be spent
* `--MaturityStd arg (=0)`                num of blocks before non-coinbase UTXO can be spent
* `--MaxBodySize arg (=1048576)`          Max block body size [bytes]
* `--DesiredRate_s arg (=60)`             Desired rate of generated blocks [seconds]
* `--DifficultyReviewCycle arg (=1440)`   num of blocks after which the mining difficulty can be adjusted
* `--MaxDifficultyChange arg (=2)`        Max difficulty change after each cycle (each step is roughly x2 complexity)
* `--TimestampAheadThreshold_s arg (=7200)` Block timestamp tolerance [seconds]
* `--WindowForMedian arg (=25)`           How many blocks are considered in calculating the timestamp median
* `--AllowPublicUtxos arg (=0)`           set to allow regular (non-coinbase) UTXO to have non-confidential signature
* `--FakePoW arg (=0)`                    Don't verify PoW. Mining is simulated by the timer. For tests only