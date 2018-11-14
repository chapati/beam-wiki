# Beam wallet protocol API (draft)

`beam-wallet` process could start with `api-port` parameter where we start *JSON-RPC 2.0* server with the following API.

## Init
Sends from client to wallet if you want to create new wallet.
``` json
{
	"jsonrpc": "2.0", 
	"method" : "init",
	"params" : 
	{
		"phrases" : "meadow;mystery;hedgehog;shoulder;robust;ice;people;snap;unique;lava;adjust;fame;",
		"pass" : "wallet_pass",		
	},
	"id" : 1234
}
```

**Result**

``` json
{
	"jsonrpc": "2.0", 
	"result" : "done",
	"id" : 1234
}
```
or with error
``` json
{
	"jsonrpc": "2.0", 
	"error" : {"code": 1, "message": "Wallet already exists."},
	"id" : 1234
}
```

## Open
Sends from client to wallet if you want to open existing wallet.
``` json
{
	"jsonrpc": "2.0", 
	"method" : "open",
	"params" : 
	{
		"pass" : "wallet_pass",		
	},
	"id" : 1234
}
```

**Result**

``` json
{
	"jsonrpc": "2.0", 
	"result" : "done",
	"id" : 1234
}
```
or with error
``` json
{
	"jsonrpc": "2.0", 
	"error" : {"code": 2, "message": "Wallet data unreadable, restore wallet.db from latest backup or delete it and reinitialize the wallet."},
	"id" : 1234
}
```

## Treasury
Sends from client to wallet if you want to generate treasury.

where

`count` - treasury UTXO count

`height` - treasury UTXO height lock step

`value` - treasury value of each UTXO (in Beams)


``` json
{
	"jsonrpc": "2.0", 
	"method" : "treasury",
	"params" : 
	{
		"count" : 30,
		"height" : 1440,
		"value" : 10
	},
	"id" : 1234
}
```

**Result**

``` json
{
	"jsonrpc": "2.0", 
	"result" : "done",
	"id" : 1234
}
```
or with error
``` json
{
	"jsonrpc": "2.0", 
	"error" : {"code": 3, "message": "Treasury not generated..."},
	"id" : 1234
}
```

## Info
Sends from client to wallet if you want to get current wallet state.
``` json
{
	"jsonrpc": "2.0", 
	"method" : "info",
	"params" : {},
	"id" : 1234
}
```

**Result**

``` json
{
	"jsonrpc": "2.0", 
	"result" : 
	{
		"summary" : 
		{
			"current_height" : 12345,
			"current_state_id" : 12345,
			"available" : 12345,
			"unconfirmed" : 12345,
			"locked" : 12345,
			"draft" : 12345,
			"available_coinbase " : 12345,
			"total_coinbase" : 12345,
			"avaliable_fee" : 12345,
			"total_fee" : 12345,
			"total_unspent" : 12345,
		},

		"tx_history" :
		[
			{
				"datetime" : 462817648736,
				"amount" : 100000,
				"status" : 2,
				"id" : 123,
			},
			...
		],

		"utxo" :
		[
			{
				"id" : 123,
				"amount" : 80,
				"height" : 1235,
				"maturity" : 3000,
				"status" : 3,
				"key_type" : 1,
			},
			...
		]
	},
	"id" : 1234
}
```
