# cosmos-validator_exporter :satellite:
![CreatePlan](https://img.shields.io/badge/relase-v0.3.0-red)
![CreatePlan](https://img.shields.io/badge/go-1.13.1%2B-blue)
![CreatePlan](https://img.shields.io/badge/license-Apache--2.0-green)

Prometheus exporter for Cosmos Validators


## Introduction
This exporter is for monitoring information which is not provided from Tendermint’s basic Prometheus exporter (localhost:26660), and other specific information monitoring purposes

## List of collected Information
- blockHeight: Height of the current block
- bondedTokens: Number of currently bonded Atom
- notBondedTokens: Number of unbonded Atom
- bondedRatio: Ratio of bonded tokens within the network
- totalSupply: Total Supply of Atom

- precommitStatus: Check precommit for previous block
- commitVoteType: Check commit vote type for previous block(false: 0, prevote: 1, precommit: 2)
- jailStatus: Confirms if the validator is jailed or not(true: 1, false: 0)

- votingPower: Decimal truncated Total voting power of the validator
- delegatorCount: Number of each unique delegators for a validator
- delegatorShares: Validator's total delegated tokens
- delegationRatio: Ratio of validator's bonded tokens to the network's total bonded tokens
- delegationSelf: Self-bonded amount of the validator
- minSelfDelegation: The required minimum number of tokens whic hthe validator must self-delegate

- Inflation: current network inflation
- actualInflation: Inflation / bondedRatio
- balances(uatom): Wallet information of the validator which shows the balance
- commission(uatom): Accumulated commission fee of the validator
- rewards(uatom): Accumulated rewards of the validator

- commissionMaxRate: The highest commission rate which the validator can charge
- commissionMaxChangeRate: Max range of commission rate whic hthe validator can change
- commissionRate: Commission rate of the validator charged on delegators' rewards

- proposerRanking: Ranking to become a proposer
- proposerStatus: Shows if the validator is the proposer or not in the current round(true: 1, false: 0)

- totalProposalCount: Total number of proposals
- votingProposalCount: Proposal that is voting period

- labels: moniker, chainId, accountAddress, operatorAddress, consHexAddress, 

## Install
```bash
mkdir exporter && cd exporter

wget https://github.com/node-a-team/cosmos-validator_exporter/releases/download/v0.3.0/cosmos-validator_exporter.tar.gz  && sha256sum cosmos-validator_exporter.tar.gz | fgrep 848594f80cb8a18e72d00f3f47bffbaea7510984857171557ec9206e59b979e7 && tar -zxvf cosmos-validator_exporter.tar.gz ||  echo "Bad Binary!"
```

## Config
1. Modify to the appropriate RPC and REST server address
2. Modify the value of ```operatorAddr``` to the operator address of the validator you want to monitor.
3. You can change the service port(default: 26661)
```bash
vi config.toml
```
```bash
# TOML Document for Cosmos-Validator Exporter(Pometheus & Grafana)

title = "TOML Document"

[Servers]
        [Servers.addr]
        rpc = "localhost:26657"
        rest = "localhost:1317"

[Validator]
operatorAddr = "cosmosvaloper14l0fp639yudfl46zauvv8rkzjgd4u0zk2aseys"

[Options]
listenPort = "26661"

```

## Start
  
```bash
./cosmos-validator_exporter {path to config.toml}

// ex)
./cosmos-validator_exporter /data/cosmos/exporter
```

## Use systemd service
  
```sh
# Make log directory & file
sudo mkdir /var/log/userLog  
sudo touch /var/log/userLog/cosmos-validator_exporter.log  
# user: cosmos
sudo chown cosmos:cosmos /var/log/userLog/cosmos-validator_exporter.log

# $HOME: /data/cosmos
# Path to config.toml: /data/cosmos/exporter
sudo tee /etc/systemd/system/cosmos-validator_exporter.service > /dev/null <<EOF
[Unit]
Description=Cosmos Validator Exporter
After=network-online.target

[Service]
User=cosmos
WorkingDirectory=/data/cosmos
ExecStart=/data/cosmos/exporter/cosmos-validator_exporter \
        /data/cosmos/exporter
StandardOutput=file:/var/log/userLog/cosmos-validator_exporter.log
StandardError=file:/var/log/userLog/cosmos-validator_exporter.log
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl enable cosmos-validator_exporter.service
sudo systemctl restart cosmos-validator_exporter.service


## log
tail -f /var/log/userLog/cosmos-validator_exporter.log
```
