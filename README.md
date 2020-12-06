[![Build Status](https://travis-ci.org/MaximilianMeister/ethereum-validator.svg?branch=master)](https://travis-ci.org/MaximilianMeister/ethereum-validator)

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

**NOTE**: Use the `files/validator_keys_testing/` directory to store your testing keys

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

### Persisting chain data/state

By default a new docker container will not persist any ETH1.0 or ETH2.0 state.

To enable persistence, you have to add the ETH1.0 and ETH2.0 datadirs to the `molecule/default/molecule.yml` file  under `platforms.[platform].volumes`

Here's an example:

```yaml
...
    volumes:
      - /sys/fs/cgroup:/sys/fs/cgroup:ro
      - /home/$USER/.ethereum:/var/lib/goethereum
      - /home/$USER/.eth2:/var/lib/prysm
...
```

Role Variables
--------------

```yaml
# SSH Port
ethv_ssh_port: "22"

# Number of validators
ethv_number_validators: "1"

# The http endpoint for an Ethereum 1.0 node (e.g. infura)
ethv_10_http_provider: "http://127.0.0.1:8545"

# Geth cache
ethv_10_cache: "1024"

# Ethereum 1.0 Chain network
ethv_10_net: ""

# Ethereum 2.0 Client network
ethv_20_net: mainnet

# Running inside docker requires systemd services to run as root
# Make sure to set this to "no" if you run on a VM or on bare-metal
ethv_docker: yes

# Use IPV6 rules for firewall
ethv_ufw_ipv6: yes

# Validator Password used during key generation
ethv_validator_password: "eth2.0-deposit-cli"

# Wallet Password used during key generation
ethv_wallet_password: "eth2.0-deposit-cli"

# String to include in proposed blocks
ethv_graffiti: "helloitsme"

# Grafana admin password
ethv_grafana_admin_password: "admin"

# Grafana protocol
ethv_grafana_protocol: "http"

# Grafana port
ethv_grafana_port: "3000"

# Grafana domain
ethv_grafana_domain: "localhost"

# Grafana certificate file
ethv_grafana_certfile: ""

# Grafana certificate key
ethv_grafana_keyfile: ""

# Grafana Mail notification smtp host (hostname:port)
ethv_grafana_smtp_host: ""

# Grafana Mail notification smtp user
ethv_grafana_smtp_user: ""

# Grafana Mail notification smtp password
ethv_grafana_smtp_password: ""

# Name of the validator_keys folder in files/ - useful if you test with multiple different ones
ethv_validator_keys_folder: "validator_keys"

# Reboot after update
ethv_reboot_after_update: yes

#
# Prysm eth2.0 client specific - https://github.com/prysmaticlabs/prysm
#

# If not built from source, the official prysm.sh installer is used
ethv_prysm_build_from_source: no

# Only used when building from source as prysm.sh pulls latest binaries
ethv_prysm_release_version: ""

# Path to the beacon-chain binary
ethv_prysm_beacon_chain_binary: "/usr/local/bin/beacon-chain"

# Path to the validator binary
ethv_prysm_validator_binary: "/usr/local/bin/validator"

# Seconds to wait before starting the service
# This acts as a slashing protection for decreasing the chance to double attestations
ethv_prysm_pre_start_delay: 780
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
