# Massa Fullnode Installation Guide

## Official Documentation
https://docs.massa.net/en/latest/testnet/install.html

## Prerequisites

### Packages
```
apt-get update \
&& apt-get install -y \
pkg-config \
curl \
git \
build-essential \
libssl-dev \
libclang-dev
```

### Rust

```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
rustc --version
rustup toolchain install nightly
rustup default nightly
rustc --version
```

## Massa Installation

### Clone repo
```
git clone --branch testnet https://github.com/massalabs/massa.git
```

### Start
```
cd massa/massa-node/
RUST_BACKTRACE=full cargo run --release -- -p <PASSWORD> |& tee logs.txt
```

On a 2nd window
```
cd massa/massa-client/
cargo run --release -- -p <PASSWORD>
```
  
### Generate keys
```
wallet_generate_secret_key
node_add_staking_secret_keys <secret key>
```

Then ask for coins in the discord faucet

```
buy_rolls <public addr> <nb of roll> <fee>
```

It is possible to make your node routable to earn more points:
```
ufw allow 31244 && ufw allow 31245
```

Last, contact the bot and register with your node address and IP


## Start node as a service
```
tee $HOME/massa-node.service > /dev/null <<EOF
[Unit]
  Description=massa-node daemon
  After=network-online.target
[Service]
  User=$USER
  WorkingDirectory=$HOME/massa/massa-node
  ExecStart=$(which cargo) run --release -- -p <password>
  Restart=on-failure
  RestartSec=3
  LimitNOFILE=65535
[Install]
  WantedBy=multi-user.target
EOF

sudo mv $HOME/massa-node.service /etc/systemd/system/

sudo systemctl enable massa-node
sudo systemctl daemon-reload
sudo systemctl restart massa-node && journalctl -u massa-node -f -o cat
```
