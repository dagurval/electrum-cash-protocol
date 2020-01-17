=======================
 Protocol Methods Extra
=======================

This attempts to document implementation specific extensions that are intended
to be widely useful. These are not part of the main specification, but may be
suitable for eventual standardization.

If the server supports any of these extensions, these must be discoverable via
the RPC call `server.features`.

blockchain.address.get_first_use
================================

Returns a first occurance of usage of scripthash as ouput on the blockchain.

See :func:`blockchain.scripthash.get_first_use`.

**Signature**

  .. function:: blockchain.scripthash.get_first_use(address)
  .. versionadded:: ElectrsCash 1.2

  * *address*

    The address as a Cash Address string (with or without prefix). Some server
    implementations may also support Legacy (base58) addresses but are not
    required to do so by this specification.

**Result**

  See :func:`blockchain.scripthash.get_balance`.


blockchain.scripthash.get_first_use
===================================

Returns a first occurance of usage of scripthash as ouput on the blockchain. Any
transaction in a confirmed block may be returned, as long as it's the first
block that contains use of scripthash.

If there are no matching transactions in the blockchain, but there are in the
mempool, one of these are returned instead. In this case, the block hash
returned is the hex representation of `0` and block height the numeric value
`0`

If there are no matching transactions in the blockchain or on the mempool, an
error is returned.

If the server supports this method if it has the key `firstuse` with
value `["1.0"]` in its `server.features` response.

Rationale for requiring the scripthash being an output is to allow wallets to
verify the response without having to fetch the input transactions. There
cannot exist earlier occurances of scripthash being used as input, an ouput
must exist first.

**Signature**

  .. function:: blockchain.block.get_first_use(scripthash)
  .. versionadded:: ElectrsCash 1.2

  *scripthash*

    The script hash as a hexadecimal string.

**Result**

  A dictionary with the following keys.

  * *height*

    The block height of the block where the first occurance of scripthash
    exists.

    Block height is 0 if the first occurance is in the mempool.

  * *block_hash*

    The block hash of the block where the first occurance of scripthash exists.

    Block hash is hex of 0 if the first occurance is in the mempool.

  * *tx_hash*

   A hash of single transaction containing the scripthash in its output.

**Example Result**

With *scripthash*
`273139c9327743dccc1030fc8d009e63e41e147c6b0449913d2dea5b71eef230` on the
Bitcoin Cash chain::

 {
    'block_hash': '000000000000000002a04f56505ef459e1edd21fb3725524116fdaedf3a4d0ab',
    'block_height': 597843,
    'tx_hash': 'cef9c1485f166e92adf0b5d42e4ad202e4f5f9e2e7a072d0a6864c61c2cd6fa0'
  }


**Standardization status**

Improvement proposal not yet written. It has been noted that requirement of
scripthash being an output would make this inefficient on some server
implementations due to database architecture.
