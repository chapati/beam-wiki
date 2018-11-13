# Beam mining protocol API

The protocol is based on JSON RPC and uses *Long Polling* to have a conversation between *Miner* and *Node*.

## Miner to Node

### Login

``` json
{
    "method" : "login", 
    "api-key" : "<string>" 
}
```

### Solution

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

``` json
{ 
    "method": "job", 
    "id": "<string>", 
    "input": "<string_base16>", 
    "difficulty": "<number>"
}
```

### Result

``` json
{
    "method" : "[login / solution]", 
    "id": "<string>", 
    "result": "[accepted / rejected / expired]", 
    "description": "<string>"
}
```