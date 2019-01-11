




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
- [validate_address](#validate_address) `done`
- [tx_send](#tx_send) `done without session`
- [tx_status](#tx_status) `done`
- [tx_split](#tx_split) `done without session`
- [tx_list](#tx_list) `done`
- [tx_cancel](#tx_cancel) `done`
- [wallet_status](#wallet_status) `done`
- [get_utxo](#get_utxo) `done`
- [lock](#lock)
- [unlock](#unlock)

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

where `lifetime` is expiration time in hours (`lifetime(0)` will never expired) and can be ommited

`<--`
```json
{
	"jsonrpc":"2.0", 
	"id": 1,
	"result":"472e17b0419055ffee3b3813b98ae671579b0ac0dcd6f1a23b11a75ab148cc67"
}
```
***

### validate_address
Just base check, validates if the address isn't garbage and belongs our elliptic curve. Also returns `is_mine == true` if address is found in the wallet DB.

`-->`
```json
{
	"jsonrpc":"2.0", 
	"id": 1,
	"method":"validate_address", 
	"params":
	{
		"address" : "472e17b0419055ffee3b3813b98ae671579b0ac0dcd6f1a23b11a75ab148cc67"
	}
}
```

`<--`
```json
{
	"jsonrpc":"2.0", 
	"id": 1,
	"result" : 
	{
		"is_valid" : true,
		"is_mine" : false,
	}
}
```
***

### tx_send
Sends transactions with specific valueand session to a given address.

`-->`
```json
{
	"jsonrpc":"2.0", 
	"id": 2,
	"method":"send", 
	"params":
	{
		"value" : 12342342,
		"fee" : 2,
		"address" : "472e17b0419055ffee3b3813b98ae671579b0ac0dcd6f1a23b11a75ab148cc67",
		"comment" : "thank you!"
	}
}
```

`fee` and `comment` can be omitted.

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
Returns transaction id or error code.
***

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
		"txId" : "10c4b760c842433cb58339a0fafef3db",
		"comment": "",
		"fee": 0,
		"kernel": "0000000000000000000000000000000000000000000000000000000000000000",
		"receiver": "472e17b0419055ffee3b3813b98ae671579b0ac0dcd6f1a23b11a75ab148cc67",
		"sender": "f287176bdd517e9c277778e4c012bf6a3e687dd614fc552a1ed22a3fee7d94f2",
		"status": 4,
		"value": 12342342,
		"height" : 1055,
		"confirmations" : 3
	} 
}
```
`height` and `confirmations` will be absent if transaction isn't in chain.
***

### tx_split
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
***

### wallet_status
Get current wallet status, height/hash/available/...

`-->`
```json
{
	"jsonrpc":"2.0", 
	"id": 6,
	"method":"wallet_status"
}
```

`<--`
```json
{
	"jsonrpc":"2.0", 
	"id": 6,
	"result":
	{
	    "current_height" : 1055,
	    "current_state_hash" : "f287176bdd517e9c277778e4c012bf6a3e687dd614fc552a1ed22a3fee7d94f2",
	    "prev_state_hash" : "bd39333a66a8b7cb3804b5978d42312c841dbfa03a1c31fc2f0627eeed6e43f2",
	    "available": 100500,
	    "receiving": 123,
	    "sending": 0,
	    "maturing": 50,
	    "locked": 30,
	}
}
```
`type` can be `fees`, `mine`, `norm`, `chng`, `...`
***

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
			"amount" : 12345,
			"height" : 5007,
			"maturity" : 60,
			"type" : "mine",
			"createTxId" : "10c4b760c842433cb58339a0fafef3db",
			"spentTxId" : "",
		}
	]
}
```
`type` can be `fees`, `mine`, `norm`, `chng`, `...`
***

### lock
`[not implemented]`

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
***

### unlock
`[not implemented]`

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
***

### tx_list
Get all the transactions with specified `status/height...`.

`-->`
```json
{
	"jsonrpc":"2.0", 
	"id": 8,
	"method":"tx_list",
	"params":
	{
		"filter" : 
		{
			"status":4,
			"height":1055,
		}
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
			"txId" : "10c4b760c842433cb58339a0fafef3db",
			"comment": "",
			"fee": 0,
			"kernel": "0000000000000000000000000000000000000000000000000000000000000000",
			"receiver": "472e17b0419055ffee3b3813b98ae671579b0ac0dcd6f1a23b11a75ab148cc67",
			"sender": "f287176bdd517e9c277778e4c012bf6a3e687dd614fc552a1ed22a3fee7d94f2",
			"status": 4,
			"value": 12342342,
			"height" : 1055,
			"confirmations" : 0
		},
		{ 
			"txId" : "10c4b760c842433cb58339a0fafef3db",
			"comment": "",
			"fee": 0,
			"kernel": "0000000000000000000000000000000000000000000000000000000000000000",
			"receiver": "472e17b0419055ffee3b3813b98ae671579b0ac0dcd6f1a23b11a75ab148cc67",
			"sender": "f287176bdd517e9c277778e4c012bf6a3e687dd614fc552a1ed22a3fee7d94f2",
			"status": 4,
			"value": 12342342,
			"height" : 1055,
			"confirmations" : 2
		}
	]
}
```
Status can be `Pending(0), InProgress(1), Cancelled(2), Completed(3), Failed(4), Registered(5)`.

`height` and `confirmations` will be absent if transaction isn't in chain.
***

### tx_cancel
Cancels running transaction, return `true` if successfully canceled or error with the reason.

`-->`
```json
{
	"jsonrpc":"2.0", 
	"id": 4,
	"method":"tx_cancel", 
	"params":
	{
		"txId" : "a13525181c0d45b0a4c5c1a697c8a7b8"
	}
}
```

`<--`
```json
{
	"jsonrpc":"2.0", 
	"id": 4,
	"result": true
}
```
***