# Aptos Indexer Fullnode - Linux
## Summary
1. Make sure that you have all the required tools and packages described below in this document.
2. Follow the instructions to set up a public fullnode but do not start the fullnode yet.
3. Edit the fullnode.yaml as described below in this document.
4. Run the indexer fullnode per the instructions below.

## Hardware requirements
- For running a production grade public fullnode:
  - CPU: 8 cores, 16 threads (Intel Xeon Skylake or newer).
  - Memory: 32GB RAM.
- For running the public fullnode for development or testing:
  - CPU: 2 cores.
  - Memory: 4GB RAM.

## Prerequisites
- [rust](https://www.rust-lang.org/tools/install)
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
```
source "$HOME/.cargo/env"
```
- [docker](https://docs.docker.com/engine/install/ubuntu/)
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
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```
- [postgres](https://www.postgresql.org/download/linux/ubuntu/)
```
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get -y install postgresql
```
- [libpq](https://howtoinstall.co/en/libpq-dev)
```
apt-get install libpq-dev
```
- [diesel](https://ubuntu.pkgs.org/20.04/ubuntu-universe-amd64/librust-diesel-derives-dev_1.4.0-3_amd64.deb.html)
```
apt install librust-diesel*
```
