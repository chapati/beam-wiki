



# Beam wallet protocol API (draft)
Wallet API will have the same structure as Node API.

Wallet will have online connection to the node. 

API will include the following methods.

## API

### create_address
Creates new receiver address.

`-->`
```json
{
	"jsonrpc":"2.0", 
	"id": 1,
	"method":"create_address", 
	"params":
	{
		"lifetime": 24,
		"metadata": "string encoded JSON metadata"
	}
}
```

where `lifetime` is expiration time in hours (`lifetime(-1)` will never expired)

`<--`
```json
{
	"jsonrpc":"2.0", 
	"id": 1,
	"result":"472e17b0419055ffee3b3813b98ae671579b0ac0dcd6f1a23b11a75ab148cc67"
}
```

### send
Sends transactions with specific valueand session to a given address.

`-->`
```json
{
	"jsonrpc":"2.0", 
	"id": 2,
	"method":"send", 
	"params":
	{
		"session" : 123,
		"value" : "1.2342342",
		"address" : "472e17b0419055ffee3b3813b98ae671579b0ac0dcd6f1a23b11a75ab148cc67" 
	}
}
```

`<--`
```json
{
	"jsonrpc":"2.0", 
	"id": 2,
	"result":
	{
		"txid" : "12345"
	}
}
```
Returns transaction id or error code (TBD)

### replace
Replaces all pending transactions for existing address with new transactions with the same value and new address.

`-->`
```json
{
	"jsonrpc":"2.0", 
	"id": 3,
	"method":"replace", 
	"params":
	{
		"existing_addr" : "472e17b0419055ffee3b3813b98ae671579b0ac0dcd6f1a23b11a75ab148cc67",
		"new_addr" : "ae671579b0ac0dcd6f1a23b11a75ab148cc67472e17b0419055ffee3b3813b98" 
	}
}
```

`<--`
```json
{
	"jsonrpc":"2.0", 
	"id": 3,
	"result":"ok"
}
```

### status
Checks status of existing transaction. Status can be `Pending(0), InProgress(1), Cancelled(2), Completed(3), Failed(4), Registered(5)`

`-->`
```json
{
	"jsonrpc":"2.0", 
	"id": 4,
	"method":"status", 
	"params":
	{
		"txid" : "123" 
	}
}
```

`<--`
```json
{
	"jsonrpc":"2.0", 
	"id": 4,
	"result":2
}
```

### split
Creates a specific set of outputs with given set of values and session.

`-->`
```json
{
	"jsonrpc":"2.0", 
	"id": 5,
	"method":"split", 
	"params":
	{
		"session" : 123,
		"outputs" : ["1.13", "2.54", "3.55"]
	}
}
```

`<--`
```json
{
	"jsonrpc":"2.0", 
	"id": 5,
	"result":""
}
```

### balance
Get current balance.

`-->`
```json
{
	"jsonrpc":"2.0", 
	"id": 6,
	"method":"balance"
}
```

`<--`
```json
{
	"jsonrpc":"2.0", 
	"id": 6,
	"result":
	{
		"available" : "123.0004",
		"in_progress" : "123.0004",
		"locked" : "123.0004"
	}
}
```

### get_utxo
Get list of all unlocked UTXOs.

`-->`
```json
{
	"jsonrpc":"2.0", 
	"id": 6,
	"method":"get_utxo"
}
```

`<--`
```json
{
	"jsonrpc":"2.0", 
	"id": 6,
	"result":
	[
		{
			"id" : 123,
			"amount" : "45.003",
			"height" : 5007,
			"maturity" : 60,
			"type" : 0
		}
	]
}
```
`type` can be `Coinbase(0), Regular(1), Comission(3)`

### lock
Create session and lock UTXOs with specified IDs.

`-->`
```json
{
	"jsonrpc":"2.0", 
	"id": 6,
	"method":"lock",
	"params":
	{
		"session" : 345,
		"ids" : [5,6,7,8]
	}
}
```

`<--`
```json
{
	"jsonrpc":"2.0", 
	"id": 6,
	"result":
	{
		"session" : 345,
		"status" : "locked"
	}
}
```

### unlock
Unlock all UTXOs for specified session.

`-->`
```json
{
	"jsonrpc":"2.0", 
	"id": 6,
	"method":"lock",
	"params":
	{
		"session" : 345
	}
}
```

`<--`
```json
{
	"jsonrpc":"2.0", 
	"id": 6,
	"result":
	{
		"session" : 345,
		"status" : "unlocked"
	}
}
```

### create_utxo
Called by the node to get new coinbase UTXO.

`-->`
```json
{
	"jsonrpc":"2.0", 
	"id": 7,
	"method":"create_utxo", 
	"params":
	{
		"value" : "80", 
		"type" : "coinbase"
	}
}
```

`<--`
```json
{
	"jsonrpc":"2.0", 
	"id": 7,
	"result":"ok"
}
```
### poll
Wallet transactions polling.

`-->`
```json
{
	"jsonrpc":"2.0", 
	"id": 8,
	"method":"poll"
}
```
`<--`
```json
{
	"jsonrpc":"2.0", 
	"id": 8,
	"result":
	{
		"txid" : "1234",
		"addr" : "472e17b0419055ffee3b3813b98ae671579b0ac0dcd6f1a23b11a75ab148cc67",
		"value" : "56.3",
		"metadata" : "<meta>custom data defined before</meta>",
		"state" : 1
	}
}
```
`state` can be:
- `received(0)` - transaction request is received.
- `failed(1)` - transaction request is failed.
- `completed(2)` - transaction request is completed.
- `confirmed(3)` - transaction request is confirmed.
