# Beam mining protocol API (draft)

The protocol is based on JSON RPC and uses *Long Polling* to have a conversation between *Miner* and *Node*. It describes only mining behavior without payouts at the moment.

## Miner to Node

### Login
Miner subscribes to the node to receive jobs.

`api-key` - miner registration key.

``` json
{
    "method" : "login", 
    "api-key" : "<string>" 
}
```

### Solution
Miner sends a solution to the node.

`nonce` - matched nonce for given solution.

`solution` - *Equhash* solition for current difficulty.

``` json
{
    "method" : "solution", 
    "id": "<string>", 
    "nonce": "<string_base16>", 
    "solution": "<string_base16>"
}
```

## Node to Miner

### Job
Miner will send new job automatically to connected miners.

`id` - is a job index, the miner should cancel the current job if get an empty *id*.

`input` - block header data hash (with current *difficulty*) as an input parameter for *Equihash*.

`difficulty` - current chain difficulty.

``` json
{ 
    "method": "job", 
    "id": "<string>", 
    "input": "<string_base16>", 
    "difficulty": "<number>"
}
```

### Result

This is what server will return to `login` or `solution` requests.

``` json
{
    "method" : "[login / solution]", 
    "id": "<string>", 
    "result": "[accepted / rejected / expired]", 
    "description": "<string>"
}
```