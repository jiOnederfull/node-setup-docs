# Aptos Indexer Fullnode - Linux

## Hardware Requirements
- For running a production grade public fullnode:
  - CPU: 8 cores, 16 threads (Intel Xeon Skylake or newer).
  - Memory: 32GB RAM.
- For running the public fullnode for development or testing:
  - CPU: 2 cores.
  - Memory: 4GB RAM.

## 1. Prerequisites
- [rust](https://www.rust-lang.org/tools/install)
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
```
source "$HOME/.cargo/env"
```
- [docker](https://docs.docker.com/engine/install/ubuntu/)
```
sudo apt-get update
```
```
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```
```
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```
```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
```
sudo apt-get update
```
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```
- [postgres](https://www.postgresql.org/download/linux/ubuntu/)
```
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get -y install postgresql postgresql-contrib
```
- [libpq](https://howtoinstall.co/en/libpq-dev)
```
apt-get install libpq-dev
```
- [diesel](https://ubuntu.pkgs.org/20.04/ubuntu-universe-amd64/librust-diesel-derives-dev_1.4.0-3_amd64.deb.html)
```
apt install librust-diesel*
cargo install diesel_cli --no-default-features --features postgres
```

##  [Set up a Public Fullnode](https://aptos.dev/nodes/full-node/fullnode-source-code-or-docker/)
```
git clone https://github.com/aptos-labs/aptos-core.git
cd aptos-core
```
```
./scripts/dev_setup.sh
```
```
source ~/.cargo/env
```
### Network
- `mainnet`
```
git checkout --track origin/mainnet
```
- `devnet`
```
git checkout --track origin/devnet
```
- `testnet`
```
git checkout --track origin/testnet
```
### fullnode.yaml
```
cp aptos-core/config/src/config/test_data/public_full_node.yaml {your directory}/fullnode.yaml
```
### Edit fullnode.yaml
- data_dir
- waypoint -> from_file
- genesis_file_location
- api -> address
- postgres_uri
```
base:
    # Update this value to the location you want the node to store its database
    data_dir: "/mnt/aptos/data"
    role: "full_node"
    waypoint:
        # Update this value to that which the blockchain publicly provides. Please regard the directions
        # below on how to safely manage your genesis_file_location with respect to the waypoint.
        from_file: "/mnt/aptos/waypoint.txt"

execution:
    # Update this to the location to where the genesis.blob is stored, prefer fullpaths
    # Note, this must be paired with a waypoint. If you update your waypoint without a
    # corresponding genesis, the file location should be an empty path.
    genesis_file_location: "/mnt/aptos/genesis.blob"

full_node_networks:
    - discovery_method: "onchain"
      # The network must have a listen address to specify protocols. This runs it locally to
      # prevent remote, incoming connections.
      listen_address: "/ip4/127.0.0.1/tcp/6180"
      network_id: "public"
      # Define the upstream peers to connect to
      seeds:
        {}


api:
    enabled: true
    address: 0.0.0.0:8080


storage:
    enable_indexer: true
    # This is to avoid the node being pruned
    storage_pruner_config:
        ledger_pruner_config:
            enable: false

indexer:
    enabled: true
    postgres_uri: "postgres://postgres@localhost:5432/postgres"
    processor: "default_processor"
    check_chain_id: true
    emit_every: 500
```
### Download genesis.blob & waypoint.txt
- `mainnet`
- genesis.blob
```
curl -O https://raw.githubusercontent.com/aptos-labs/aptos-networks/main/mainnet/genesis.blob
```
- waypoint.txt
```
curl -O https://raw.githubusercontent.com/aptos-labs/aptos-networks/main/mainnet/waypoint.txt
```
- `devnet`
- genesis.blob
```
wget -O genesis.blob https://raw.githubusercontent.com/aptos-labs/aptos-networks/main/devnet/genesis.blob
```
- waypoint.txt
```
wget -O waypoint.txt https://raw.githubusercontent.com/aptos-labs/aptos-networks/main/devnet/waypoint.txt
```
- `testnet`
- genesis.blob
```
wget -O genesis.blob https://raw.githubusercontent.com/aptos-labs/aptos-networks/main/testnet/genesis.blob
```
- waypoint.txt
```
wget -O waypoint.txt https://raw.githubusercontent.com/aptos-labs/aptos-networks/main/testnet/waypoint.txt
```

## Set up Database
```
cd {your directory}/aptos-core/crates/indexer
diesel migration run --database-url postgresql://localhost/postgres
```


Start the node!
