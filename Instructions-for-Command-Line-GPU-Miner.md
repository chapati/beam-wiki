# Running a GPU miner

* [Miner source code](https://github.com/BeamMW/beam/tree/master/pow)
* Miner connects to Beam Node with Stratum server and performs mining jobs. It can be run on any amount of machines with GPU.


# Command line execution example:

``` sh
./miner_client --server {node_host}:{stratum_port} --key {api_key}
```

# Known limitation:
At this time only single GPU per miner client is supported

# See also:
[Instructions for Command Line Node](https://github.com/BeamMW/beam/wiki/Instructions-for-Command-Line-Node) and scroll down to "Running Beam Node with Stratum Server"

