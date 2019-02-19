# Exchange integration guide (DRAFT)

## Getting binaries
First of all, you should decide on which network you want to play with Beam:
- `mainnet` - it's the latest released version and you can get binaries from the official website https://www.beam.mw/downloads, from Github Releases https://github.com/BeamMW/beam/releases or build yourself from the sources ([mainnet branch](https://github.com/BeamMW/beam/tree/mainnet)).
- `testnet` - download from Github Releases https://github.com/BeamMW/beam/releases or build from the [testnet branch](https://github.com/BeamMW/beam/tree/testnet).
- `master` - here you can test the latest changes we are working on, build it from the [master branch](https://github.com/BeamMW/beam/tree/master).

Here are detailed instructions on how to build project for *Windows*, *Linux*, *Mac* platforms https://github.com/BeamMW/beam/wiki/How-to-build.
> Add `-DBEAM_NO_QT_UI_WALLET=On` command line parameter to the Cmake if you need only CLI version of the wallet without UI and QT5 library dependencies.

At the end you will have to get four binaries: `beam-node`, `explorer-node`, `beam-wallet` and `wallet-api`.
 
## Initializing Wallet
To create a new wallet run the following command:  
`./beam-wallet init`  
and `wallet.db` will be created in the working folder, as output you will get something like
```
I 2019-02-19.15:28:55.500 Beam Wallet 1.2.4392 (master)
I 2019-02-19.15:28:55.501 Rules signature: 7c360d0c2ee92d9e
I 2019-02-19.15:28:55.501 starting a wallet...
Enter password: ***
Confirm password: ***
I 2019-02-19.15:29:01.716 Generating seed phrase...
======
Generated seed phrase:

        army;wing;very;trim;crumble;meat;spawn;click;donate;loyal;trap;gauge;

        IMPORTANT

        Your seed phrase is the access key to all the cryptocurrencies in your wallet.
        Print or write down the phrase to keep it in a safe or in a locked vault.
        Without the phrase you will not be able to recover your money.
======
I 2019-02-19.15:29:01.950 wallet successfully created...
I 2019-02-19.15:29:01.971 New address generated:

1f27eea1057633e7df23aa51f835a05fa759ca199535fe99952bf9aecad17186d9d

I 2019-02-19.15:29:01.971 comment = default
```
Once wallet is initialised, you have to export your *Owner Key* (it will be needed in the future to start own node).
```
$ ./beam-wallet export_owner_key
I 2019-02-19.15:32:16.217 Beam Wallet 1.2.4392 (master)
I 2019-02-19.15:32:16.218 Rules signature: 7c360d0c2ee92d9e
I 2019-02-19.15:32:16.219 starting a wallet...
Enter password: ***
Owner Viewer key: +SevBZ++xL1wEM+yyGbMI+ZElHahudX8mh6Hu/atJrdtzAOD2zpeb2LPIqQcnvry3JUQFBa9gTAHT98RMQMdcggr+LX0oqdGsVIx3KRkTxyvRdKBnw8lz9uAmMx0P2TNlk30E+M5MCnX7Ngp
```

## Starting a Node
Run the node with your _Owner Key_ and make sure it has completed the synchronization with the network:  
`./beam-node.exe --peer=eu-node01.mainnet.beam.mw:8100 --key_owner=XPWoJ/ViEO1whRcYC1z/nylDH1C2lqLxpMYU0/AbqP67XI4sgYDkUIvJfUpMFjwdPYNz2A7PCHWo7c/kHHTZ2EDUNv2BJvQHb1KHZjLNZPFgV2wceHfzvCYIUF3cR9ADfVSBquTxEldipNgp`  
Please, make sure you pass proper peer address for the current network you chosen before.
```
MASTER peers:
eu-node01.masternet.beam.mw:8100
eu-node02.masternet.beam.mw:8100
eu-node03.masternet.beam.mw:8100
eu-node04.masternet.beam.mw:8100

TESTNET peers:
ap-node01.testnet.beam.mw:8100
ap-node02.testnet.beam.mw:8100
ap-node03.testnet.beam.mw:8100
eu-node01.testnet.beam.mw:8100
eu-node02.testnet.beam.mw:8100
eu-node03.testnet.beam.mw:8100
us-node01.testnet.beam.mw:8100
us-node02.testnet.beam.mw:8100
us-node03.testnet.beam.mw:8100

MAINNET peers:
eu-node01.mainnet.beam.mw:8100
eu-node02.mainnet.beam.mw:8100
eu-node03.mainnet.beam.mw:8100
eu-node04.mainnet.beam.mw:8100
us-node01.mainnet.beam.mw:8100
us-node02.mainnet.beam.mw:8100
us-node03.mainnet.beam.mw:8100
us-node04.mainnet.beam.mw:8100
ap-node01.mainnet.beam.mw:8100
ap-node02.mainnet.beam.mw:8100
ap-node03.mainnet.beam.mw:8100
ap-node04.mainnet.beam.mw:8100
```

## Starting Wallet API
With API you can check current wallet status/balance, get all you utxo/transactions list, create/verify address and send money.

There are two ways to send JSON RPC commands to the API, using TCP socket or via HTTP calls. With TCP you can hold only one connection, send JSON commands separated with `\n` symbol and receive callbacks from the API, in the one hand it's more flexible but a bit difficult to implement in the other hand. With HTTP you can do POST requests using CURL, for example.  
So, to start API with HTTP support use the command:  
`./wallet-api --node_addr=x.x.x.x:port --api_use_http=1`  
where `node_addr` is your node address and port.

Here is detailed wallet API documentation https://github.com/BeamMW/beam/wiki/Beam-wallet-protocol-API.

## Starting Node Explorer API
With the *Node Explorer API* you can get information about current chain state and blocks using simple GET requests.
To run explorer use the command:  
`./explorer-node.exe --peer eu-node01.mainnet.beam.mw:8100 --api_port=8080`  
Here is detailed explorer API documentation https://github.com/BeamMW/beam/wiki/Beam-Node-Explorer-API