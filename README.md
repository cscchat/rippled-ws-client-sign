# casinocoind-ws-client-sign

#### Sign transactions locally and submit with `casinocoind-ws-client`

This is a ES6 module to complement **[casinocoind-ws-client](https://www.npmjs.com/package/casinocoind-ws-client)**. This module allows you to:

1. Sign a transaction **offline** and return the `tx_blob` and `tx_id` (useful for air gapped transactions) 😎
2. Submit a pre-signed transaction (`tx_blob` and `tx_id`)
3. Sign and submit a transaction 🎉

## Awesome features

- If you are signing and submitting online, there's **no need** to enter the account `Sequence`, the `LastLedgerSequence` and/or the `Fee`: if you don't enter them the class will find the right values for you.
- Submitting a transaction (from pre-signed `tx_blob` or by signing one) returns a _promise_. The class will handle watching the ledger for you, so the promise will either **resolve** because the transaction is in a validated ledger, of **reject** because there of an error or the Leder Index is past the entered / auto generated LastLedgerSequence.

### Samples are available [over here](https://github.com/cscchat/casinocoind-ws-client-sign/blob/master/samples)

## How to use

To use this module in vanillajs, vue-webpack, nodejs, etc.: please check [the docs for casinocoind-ws-client](https://github.com/cscchat/casinocoind-ws-client#readme): same thing for casinocoind-ws-client-sign.

### Basic example of signing and submmitting a transaction

```
const casinocoindWsClient = require('casinocoind-ws-client')
const casinocoindWsClientSign = require('casinocoind-ws-client-sign')

let Seed = 'sXXXXXXXXXXXXX' // (keypair supported as well)

const Transaction = {
  TransactionType: 'Payment',
  Account: 'cXXXXXXXXX..',
  Destination: 'cYYYYYYYYY..',
  DestinationTag: 1337,
  Amount: 0.25 * 1000000, // Amount in drops, so multiply (6 decimal positions)
  LastLedgerSequence: null // Null = auto detect, last + 5
}

new casinocoindWsClient('wss://ws01.casinocoin.org:4443').then((Connection) => {
  new casinocoindWsClientSign(Transaction, Seed, Connection).then((TransactionSuccess) => {
    console.log('TransactionSuccess', TransactionSuccess)
    Connection.close()
  }).catch((SignError) => {
    console.log('SignError', SignError.details) // .details ;)
    Connection.close()
  })
}).catch((ConnectionError) => {
  console.log('ConnectionError', ConnectionError)
})
```

## Errors

This class rejects a `casinocoindWsClientSignError`-error. This error is identical to `Error`, but adds the `.details` property. In `.details` additional information about the Error is available (e.g. the response from the casinocoind-server).

#### The following errors (...`.details.type`) exist:

##### Connectivity / Account
- `invalid_wsclient`
- `subscribe_error`
- `account_info_invalid`
- `account_info_error`

##### Seed / keypair
- `seed_invalid`
- `keypair_invalid_keys`
- `keypair_invalid_hex`
- `keypair_invalid`

##### Transaction contents
- `transaction_invalid`
- `transaction_invalid_no_signed_object`
- `invalid_transaction_json`
- `invalid_transaction_type`
- `invalid_transaction_jsonstring`

##### Offline / Fee / Sequence requirements
- `sequence_required_offline`
- `fee_required_offline`
- `sequence_not_a_number`

##### Transaction Sign / Submit / Response
- `transaction_error`
- `transaction_submit_error`
- `transaction_submit_non_tes_or_queued`
- `sign_error`

# Security

**This module will _ALWAYS_ sign locally / client-side.**

**Your seed / secret / Private Key will _NEVER_ be sent across the WebSocket / internet 🎉**
