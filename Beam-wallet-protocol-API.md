# Beam wallet protocol API (draft)

Wallet API will have the same structure as Node API.

Wallet will have online connection to the node.

## How to use

Find `wallet-api` binary in the `wallet` subdirectory, place it near your `wallet.db` file and run with the following arguments:

```
Wallet API options:
  -h [ --help ]                  list of all options
  -p [ --port ] arg (=10000)     port to start server on
  -n [ --node_addr ] arg         address of node
  --wallet_path arg (=wallet.db) path to wallet file
  --pass arg                     password for the wallet
```

`./wallet-api --node_addr=172.104.249.212:8101 --pass=123` for example.

### Demo code
Here is a code on NodeJS to get all the UTXOs, for example:

```js
var net = require('net');

var client = new net.Socket();
client.connect(10000, '127.0.0.1', function() {
	console.log('Connected');
	client.write(JSON.stringify(
		{
			jsonrpc: '2.0',
			id: 123,
			method: 'get_utxo',
			params: {}
		}) + '\n');
});

var acc = '';

client.on('data', function(data) {
	acc += data;

	// searching for \n symbol to find end of response
	if(data.indexOf('\n') != -1)
	{
		var res = JSON.parse(acc);

		console.log('Received:', res);

		client.destroy(); // kill client after server's response
	}
});

client.on('close', function() {
	console.log('Connection closed');
});
```


## API

API will include the following methods:

- [create_address](#create_address) `done`
- [send](#send) `done without session`
- [replace](#replace)
- [tx_status](#tx_status) `done`
- [split](#split) `done without session`
- [balance](#balance) `done`
- [get_utxo](#get_utxo) `done`
- [lock](#lock)
- [unlock](#unlock)
- [tx_list](#tx_list)

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

where `lifetime` is expiration time in hours (`lifetime(0)` will never expired)

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
		"value" : 12342342,
		"fee" : 2,
		"address" : "472e17b0419055ffee3b3813b98ae671579b0ac0dcd6f1a23b11a75ab148cc67",
		"comment" : "thank you!"
	}
}
```

`session`, `fee` and `comment` can be omitted.

`<--`
```json
{
	"jsonrpc":"2.0", 
	"id": 2,
	"result":
	{
		"txId" : "10c4b760c842433cb58339a0fafef3db"
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

### tx_status
Checks status of existing transaction. Status can be `Pending(0), InProgress(1), Cancelled(2), Completed(3), Failed(4), Registered(5)`

`-->`
```json
{
	"jsonrpc":"2.0", 
	"id": 4,
	"method":"tx_status", 
	"params":
	{
		"txId" : "10c4b760c842433cb58339a0fafef3db" 
	}
}
```

`<--`
```json
{
	"jsonrpc":"2.0", 
	"id": 4,
	"result":
	{ 
		"comment": "",
		"fee": 0,
		"kernel": "0000000000000000000000000000000000000000000000000000000000000000",
		"receiver": "472e17b0419055ffee3b3813b98ae671579b0ac0dcd6f1a23b11a75ab148cc67",
		"sender": "f287176bdd517e9c277778e4c012bf6a3e687dd614fc552a1ed22a3fee7d94f2",
		"status": 4,
		"value": 12342342 
	} 
}
```

### split
Creates a specific set of outputs with given set of values and session.

The session parameter is important in the following case:

Let's say you need to do a payout to a 1000 people, each with different amount. First you split the UTXOs you have using the 'split' method but then if you want to make another transaction you want to make sure these UTXOs are not used for any other payout. To do that you 'lock' them with the session id during split, and they will only be spent of the specific session id is provided in the 'send' method.

`-->`
```json
{
	"jsonrpc":"2.0", 
	"id": 5,
	"method":"split", 
	"params":
	{
		"session" : 123,
		"coins" : [11, 12, 13, 50000000],
		"fee" : 3
	}
}
```

`<--`
```json
{
	"jsonrpc":"2.0", 
	"id": 5,
	"result":
	{
		"txId" : "10c4b760c842433cb58339a0fafef3db"
	}
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
			"type" : "mine"
		}
	]
}
```
`type` can be `fees`, `mine`, `norm`, `chng`, `...`

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
### tx_list
Get all the transactions with specified `id/status/...`.

`-->`
```json
{
	"jsonrpc":"2.0", 
	"id": 8,
	"method":"tx_list",
	"params":
	{
		"filter" : {"status":4}
	}
}
```
`<--`
```json
{
	"jsonrpc":"2.0", 
	"id": 8,
	"result":
	[
		{
			"txId" : "1234",
			"addr" : "472e17b0419055ffee3b3813b98ae671579b0ac0dcd6f1a23b11a75ab148cc67",
			"value" : "56.3",
			"metadata" : "<meta>custom data defined before</meta>",
			"state" : 1		
		},
		{
			"txId" : "1234",
			"addr" : "472e17b0419055ffee3b3813b98ae671579b0ac0dcd6f1a23b11a75ab148cc67",
			"value" : "56.3",
			"metadata" : "<meta>custom data defined before</meta>",
			"state" : 1		
		},
	]
}
```
`state` can be:
- `received(0)` - transaction request is received.
- `failed(1)` - transaction request is failed.
- `completed(2)` - transaction request is completed.
- `confirmed(3)` - transaction request is confirmed.
