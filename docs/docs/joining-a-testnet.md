# Joining a testnet

In order to join the test network you will need to have the correct version of the fetchd ledger available on your system. 

## Using a local version

Assuming that you have followed the [installation guide](../building/). You should now have `fetchd` successfully installed in your path. You can check this with the following command:

```bash
fetchd version
```

This should print a version number that must be compatible with the network you're connecting to (see the [network page](../networks/) for the list of supported versions per network).

### Configuring the client fetchd

In general to configure the CLI to point at a given network it needs as a minimum the following configuration values

```bash
fetchd config chain-id <chain-id>
fetchd config node <rpc url>
```

### Capricorn example

In the case of the Capricorn network this would be as follows:

```bash
fetchd config chain-id capricorn-1
fetchd config node https://rpc-capricorn.fetch.ai:443
```

### Configuring the server `fetchd`

Initialize fetchd by running command. This setups a default / empty genesis configuration.

```bash
fetchd init <moniker-name> --chain-id <chain id>
```

> This will initialize default configuration files under the `FETCHD_HOME` folder, which default to `~/.fetchd/`. 

Execute the following command to download the latest genesis file:

```bash
curl <rpc url>/genesis | jq '.result.genesis' > ~/.fetchd/config/genesis.json
```

Finally connect fetchd to the network by getting it to connect to a seed node for the given network.

```bash
fetchd start --p2p.seeds=<network seed peers>
```

**Capricorn Example**

Less abstractly then, if you wants to connect to the Capricorn test net for example, you would need to run the following steps:

```bash
# init
fetchd init my-first-fetch-node --chain-id capricorn-1

# genesis
curl https://rpc-capricorn.fetch.ai:443 | jq '.result.genesis' > ~/.fetchd/config/genesis.json

# start
fetchd start --p2p.seeds=fec822ecf6e503a694a709ce663fd0c6da5fda3e@connect-capricorn.fetch.ai:36956
```

Your local node will then start to synchronise itself with the network, replaying all blocks and transactions up to the current block. Depending on the age of the network and your hard disk speed, this could take a while. 

To know when your node as finished syncing, you can query it's status from its RPC API:

```bash
curl -s 127.0.01:26657/status |  jq '.result.sync_info.catching_up'
true # this will print "false" once your node is up to date
```