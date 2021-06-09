# experimental-network genesis archive

This repo can be used to produce the `genesis.blob` file needed to join the "0L experimental-network" (chain id `1`).

For reference purposes the resulting binary can be found under `/genesis/genesis.blob`, along with the corresponding `genesis_waypoint` hash.

Producing the genesis file requires the 0L Move codebase (standard library and transaction scripts) to be at the same version from the time of genesis: v4.2.2. 

### Create a gensis file

The tools required are `libra-genesis-tool` and `stdlib`, which will both found at the git tag `v4.2.2`. Note: ignore the rust cargo toml number versions which will differ.

Note: using a different standard library other than that at v4.2.2 will yield an incorrect waypoint, and you will not be able to sync to the experimental network.

```
# compile the move Stdlib
cargo run -p stdlib --release

# build the genesis
cargo run -p libra-genesis-tool --release -- genesis \
--chain-id ${CHAIN_ID} \
--shared-backend ${REMOTE} \
--path ${DATA_PATH}/genesis.blob

```

### Verify a genesis file
If you are joining as a fullnode or validator node, you may confirm your local key_store.json file contains the correct waypoint in the genesis.blob with:

```
cargo run -p libra-genesis-tool --release -- verify \
--validator-backend 'backend=disk;path=/your/ol/root/path/key_store.json;namespace=<your_account>' \
--genesis-path path/to/genesis.blob
```

### Auditing genesis
A hybrid release `v4.3.2-genesis-retrofit` ([link](https://github.com/OLSF/libra/tree/v4.3.2-genesis-retrofit)) exists for the purpose of auditing genesis with the benefit of numerous bugfixes and tooling enhancements. It is based on v4.3.2, the lastest version to be compatible with the Move language spec from the time of genesis. The release mainly retrofits `language/toolsvm-genesis/lib.rs`, to be able to execute all the Move code from v4.2.2.
