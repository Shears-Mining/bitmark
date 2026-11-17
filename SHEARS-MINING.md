# Shears Mining changes to Bitmark 0.9.7.4

Built and running on the Shears Mining pool since 17 November 2026. No consensus change:
these nodes stay on exactly the same chain as every other Bitmark node.

## New RPC commands (merge-mining paid to any address)

- `createauxblock <address>` - builds a merge-mining block whose coinbase pays `<address>`
  and returns the same fields as `getauxblock` (hash, chainid, previousblockhash,
  coinbasevalue, bits, height, target).
- `submitauxblock <hash> <auxpow>` - submits a solved auxpow for a block from
  `createauxblock`. Returns true if accepted.

`getauxblock` still works as before (pays the node's own wallet). With the new pair a
pool can give every miner work that pays their own Bitmark address.

## Build fixes for modern compilers (GCC 13, Ubuntu 24.04)

- `-fno-strict-aliasing` added in configure.ac. Without it GCC 13 mis-compiles the old
  Lyra2REv2 code: the hash comes out different on every run, and the node refuses to
  start ("Corrupted block database detected") at the first Lyra2 block it checks.
- `blake2.h`: the `ALIGN(x)` attribute is now empty. GCC 13 rejects the 64-byte
  alignment inside the packed structs ("size of array element is not a multiple of its
  alignment"). The hash results are unchanged.

Configure used: `./configure --with-incompatible-bdb --without-gui --disable-tests
--without-miniupnpc CXXFLAGS="-O2 -std=gnu++14 -include cstdint -DBOOST_BIND_GLOBAL_PLACEHOLDERS"`
(needs libsodium-dev and the usual boost / libdb5.3++ / libssl packages).
