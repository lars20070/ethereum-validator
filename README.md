ethereum-validator
=========

Setup an Ethereum 2.0 validator node for staking with:

- ETH 1.0 client (geth)
- ETH 2.0 Prysm Beacon Chain
- ETH 2.0 Prysm Validator
- Prometheus (Monitoring API)
- Grafana (Monitoring Dashboard)

Requirements
------------

Before you run this role, you need to generate the keys for your validator and copy them into the files folder.

```bash
git clone https://github.com/ethereum/eth2.0-deposit-cli.git
cd eth2.0-deposit-cli
sudo ./deposit.sh install
./deposit.sh new-mnemonic --num_validators <numberofvalidators> --mnemonic_language=english --chain <chain>
cp ./validator_keys/*.json $THIS_ANSIBLE_REPO/files/validator_keys/
```

The reason this is not automated in this role, even though it's possible, is that this command will display your mnemonic seed phrase which you have to write down and store in a safe place. It would be required to buffer the seed phrase and log it to the ansible user to write it down.

Thanks:

**Somer Esat** for the [guide](https://someresat.medium.com/guide-to-staking-on-ethereum-2-0-ubuntu-medalla-prysm-4d2a86cc637b) that led to this ansible role.
**Guillaume Mirralles** for the [grafana dashboard](https://raw.githubusercontent.com/GuillaumeMiralles/prysm-grafana-dashboard/master/less_10_validators.json).

Run it with molecule
--------------------

## Docker

The default deployment requires docker to be installed

```
python3 -m pip install "molecule[ansible]"
python3 -m pip install "molecule[docker,lint]"
```

### Installing a full Ethereum validator node

```
# Create the docker container
molecule create

# Run the installation process
molecule converge
```

### Run a full testsuite

`molecule test`

Role Variables
--------------

```yaml
# Number of validators
ethv_number_validators: "1"

# Ethereum 1.0 Chain network
ethv_10_net: goerli

# Ethereum 2.0 Client network
ethv_20_net: pyrmont

# Running inside docker requires systemd services to run as root
ethv_docker: yes

# External Node IP
ethv_node_ip: ""

# Validator Password used during key generation
ethv_validator_password: "eth2.0-deposit-cli"

# Wallet Password used during key generation
ethv_wallet_password: "eth2.0-deposit-cli"

# String to include in proposed blocks
ethv_graffiti: "helloitsme"

#
# Prysm eth2.0 client specific - https://github.com/prysmaticlabs/prysm
#

# If not built from source, the official prysm.sh installer is used
ethv_prysm_build_from_source: no

# Path to the beacon-chain binary
ethv_prysm_beacon_chain_binary: "/usr/local/bin/beacon-chain"

# Path to the validator binary
ethv_prysm_validator_binary: "/usr/local/bin/validator"
```

Dependencies
------------

N/A

Example Playbook
----------------

    - hosts: servers
      roles:
         - { role: ethereum-validator }

License
-------

MIT

Author Information
------------------

Maximilian Meister
