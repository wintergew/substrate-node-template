# Polkadot Substrate Development Final Project

The following are the steps I've been taken during the making of my final project.

## Table of contents

- [Polkadot Substrate Development Final Project](#polkadot-substrate-development-final-project)
  * [1. Building a blockchain](#1-building-a-blockchain)
  * [2. Simulating a substrate network](#2-simulating-a-substrate-network)
  * [3. Adding trusted nodes to a network.](#3-adding-trusted-nodes-to-a-network)
    + [Generating Aura and Grandpa keys for Account 1](#generating-aura-and-grandpa-keys-for-account-1)
    + [Generating Aura and Grandpa keys for Account 2](#generating-aura-and-grandpa-keys-for-account-2)
    + [Custom Spec](#custom-spec)
    + [Adding Account 1 keys to keystore](#adding-account-1-keys-to-keystore)
    + [Adding Account 2 keys to keystore](#adding-account-2-keys-to-keystore)
    + [Starting the nodes](#starting-the-nodes)
  * [8. Smart contracts](#8-smart-contracts)
    + [Preparing the environment](#preparing-the-environment)
    + [Creating the smart contract](#creating-the-smart-contract)
    + [Deploying the smart contract](#deploying-the-smart-contract)
    + [Interacting with the smart contract](#interacting-with-the-smart-contract)

## 1. Building a blockchain

1. Forked the substrate-node-template repository
2. Switched to a new branch using `git switch -c my-learning-branch`
3. Built and run the node using `cargo build --release` and `./target/release/node-template --dev`.
4. Forked the frontend template to [my profile](https://github.com/wintergew/substrate-front-end-template)
5. Installed and run the frontend by `yarn install` and `yarn run`
6. Demonstrated a fund transfer on the node using the frontend template.

## 2. Simulating a substrate network

1. First run the `./target/release/node-template purge-chain --base-path /tmp/alice --chain local` to purge the chain for alice and remove the data (It gave an error first time because chain for alice has not been created yet).
2. Started the local chain for alice by running

   ```
   ./target/release/node-template \
   --base-path /tmp/alice \
   --chain local \
   --alice \
   --port 30333 \
   --rpc-port 9933 \
   --node-key 0000000000000000000000000000000000000000000000000000000000000001 \
   --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \
   --validator
   ```

   The `--ws-port 9945` argument is not used because it has been deprecated.
3. Run the `./target/release/node-template purge-chain --base-path /tmp/bob --chain local` to purge the chain for bob and remove the data (It gave an error first time because chain for bob also has not been created yet).
4. Start the node for bob with

   ```
   ./target/release/node-template \
   --base-path /tmp/bob \
   --chain local \
   --bob \
   --port 30334 \
   --rpc-port 9934 \
   --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \   
   --validator \
   --bootnodes /ip4/127.0.0.1/tcp/30333/p2p/12D3KooWEyoppNCUx8Yx66oV9fJnriXwCcXwDDUA2kj6vnc6iDEp
   ```

   Provided the local node identity in the command for bob "12D3KooWEyoppNCUx8Yx66oV9fJnriXwCcXwDDUA2kj6vnc6iDEp"
5. Both Alice and Bob's node has discovered each other outputted this on their consoles

   ```
   discovered: 12D3KooWEyoppNCUx8Yx66oV9fJnriXwCcXwDDUA2kj6vnc6iDEp /ip4/192.168.1.40/tcp/30333
   ```

   and the

   ```
   💤 Idle (0 peers), best: #0 (0x2f6a…8b1a), finalized #0 (0x2f6a…8b1a), ⬇ 0 ⬆ 0  
   ```

   changes to (notice the peer count);

   ```
   💤 Idle (1 peers), best: #2 (0x290d…a070), finalized #0 (0x2f6a…8b1a), ⬇ 0.6kiB/s ⬆ 0.6kiB/s   
   ```

   and they start to produce blocks.

## 3. Adding trusted nodes to a network.

Generated 2 accounts with aura and grandpa keys for this chapter.

### Generating Aura and Grandpa keys for Account 1

For demonstration purposes, I chose `123456` as a password for this account. Also, I omitted the `--password-interactive` and used `--password` to directly specify the password.

1. First I run this command to generate aura key.

```
./target/release/node-template key generate --scheme Sr25519 --password 123456
```

which outputted;

```
Secret phrase:       maid such focus despair coffee consider trade half test net zoo forum
  Network ID:        substrate
  Secret seed:       0x4a108f68d2517f230da7a2daad9f7db23187375c6d483adf7a2d60e3d0c78930
  Public key (hex):  0x3c10b265e8fc7b7c9d0db164504acfd91313d5be57a11f91c81b9e122e3b3201
  Account ID:        0x3c10b265e8fc7b7c9d0db164504acfd91313d5be57a11f91c81b9e122e3b3201
  Public key (SS58): 5DRTgzFPm182YBe1TfiafaTVJaKd796uy2gHJAZA8ibbHsZh
  SS58 Address:      5DRTgzFPm182YBe1TfiafaTVJaKd796uy2gHJAZA8ibbHsZh

```

2. Next, I used the following command to create grandpa key. Again, I omitted the `--password-interactive` and used `--password` to directly specify the password and also replaced the secret phrase with the one in the previous step.

```
./target/release/node-template key inspect --password 123456 --scheme Ed25519 "maid such focus despair coffee consider trade half test net zoo forum"
```

which outputted;

```
Secret phrase:       maid such focus despair coffee consider trade half test net zoo forum
  Network ID:        substrate
  Secret seed:       0x4a108f68d2517f230da7a2daad9f7db23187375c6d483adf7a2d60e3d0c78930
  Public key (hex):  0x77a94725c463f13cba0c1ee5d1b6243e77624d59488a1e4cbcbdb96a208ff690
  Account ID:        0x77a94725c463f13cba0c1ee5d1b6243e77624d59488a1e4cbcbdb96a208ff690
  Public key (SS58): 5Embqm1hHuDK6WQApPRUzU5iE3oHKoLF4jn8Gfb8imhNrZb5
  SS58 Address:      5Embqm1hHuDK6WQApPRUzU5iE3oHKoLF4jn8Gfb8imhNrZb5
```

### Generating Aura and Grandpa keys for Account 2

I followed the same steps as the Account 1 but used a different password `qwerty` for demonstration;

1. First I run this command to generate aura key.

```
./target/release/node-template key generate --scheme Sr25519 --password qwerty
```

which outputted;

```
Secret phrase:       blouse rescue pepper spirit disease cluster priority fence soap they sketch depart
  Network ID:        substrate
  Secret seed:       0xce251ff6a44ce31541ea4c00dc1eca94a5a26d51de59a513d53f1c390128287a
  Public key (hex):  0xe629375abe433188759254b94784d28899a413858c03708dbc9044114149891e
  Account ID:        0xe629375abe433188759254b94784d28899a413858c03708dbc9044114149891e
  Public key (SS58): 5HGV7bAKPKzEvfv77PARZKAaDo9NVxch7ZbUG1cv4cxpNAfG
  SS58 Address:      5HGV7bAKPKzEvfv77PARZKAaDo9NVxch7ZbUG1cv4cxpNAfG
```

2. Next, I used the following command to create grandpa key.

```
./target/release/node-template key inspect --password qwerty --scheme Ed25519 "blouse rescue pepper spirit disease cluster priority fence soap they sketch depart"
```

which outputted;

```
Secret phrase:       blouse rescue pepper spirit disease cluster priority fence soap they sketch depart
  Network ID:        substrate
  Secret seed:       0xce251ff6a44ce31541ea4c00dc1eca94a5a26d51de59a513d53f1c390128287a
  Public key (hex):  0xb3cba8dbffff0158501bff528fb222c43e291edef0e245d678fa2689227a6fda
  Account ID:        0xb3cba8dbffff0158501bff528fb222c43e291edef0e245d678fa2689227a6fda
  Public key (SS58): 5G8SvnFqg2kmPEHWC4BVB1XkEZ1ksY1oUgXgsLBw5mipzTLx
  SS58 Address:      5G8SvnFqg2kmPEHWC4BVB1XkEZ1ksY1oUgXgsLBw5mipzTLx
```

### Custom Spec

1. I exported the custom spec json using the following command;

```
./target/release/node-template build-spec --disable-default-bootnode --chain local > customSpec.json
```

2. Changed the name `"name": "Local Testnet"` to `"name": "Learning Testnet"`
3. Added the generated accounts as authorities in the commit [089c777](https://github.com/wintergew/substrate-node-template/commit/089c777d799ed79142a82f12dfed87617d7c5acc);

```
"aura": {
        "authorities": [
          "5DRTgzFPm182YBe1TfiafaTVJaKd796uy2gHJAZA8ibbHsZh",
          "5HGV7bAKPKzEvfv77PARZKAaDo9NVxch7ZbUG1cv4cxpNAfG"
        ]
      },
      "grandpa": {
        "authorities": [
          [
            "5Embqm1hHuDK6WQApPRUzU5iE3oHKoLF4jn8Gfb8imhNrZb5",
            1
          ],
          [
            "5G8SvnFqg2kmPEHWC4BVB1XkEZ1ksY1oUgXgsLBw5mipzTLx",
            1
          ]
        ]}
```

4. Converted the custom spec to raw custom spec in [0248304](https://github.com/wintergew/substrate-node-template/commit/0248304305f59fe38aabd4de57ff18ba945c0f85) using;

```
./target/release/node-template build-spec --chain=customSpec.json --raw --disable-default-bootnode > customSpecRaw.json
```

### Adding Account 1 keys to keystore

1. Added the Account 1 aura keys to the node01 keystore. Added the secret phrase to the suri argument and added the password in the password argument instead of using the interactive password.

```
./target/release/node-template key insert --base-path /tmp/node01 \
  --chain customSpecRaw.json \
  --scheme Sr25519 \
  --suri "maid such focus despair coffee consider trade half test net zoo forum" \
  --password 123456 \
  --key-type aura
```

2. Added the Account 1 grandpa keys to the node01 keystore.

```
./target/release/node-template key insert --base-path /tmp/node01 \
  --chain customSpecRaw.json \
  --scheme Ed25519 \
  --suri "maid such focus despair coffee consider trade half test net zoo forum" \
  --password 123456 \
  --key-type gran
```

### Adding Account 2 keys to keystore

1. Added the Account 2 aura keys to the node02 keystore.

```
./target/release/node-template key insert --base-path /tmp/node02 \
  --chain customSpecRaw.json \
  --scheme Sr25519 \
  --suri "blouse rescue pepper spirit disease cluster priority fence soap they sketch depart" \
  --password qwerty \
  --key-type aura
```

1. Added the Account 1 grandpa keys to the node01 keystore.

```
./target/release/node-template key insert --base-path /tmp/node02 \
  --chain customSpecRaw.json \
  --scheme Ed25519 \
  --suri "blouse rescue pepper spirit disease cluster priority fence soap they sketch depart" \
  --password qwerty \
  --key-type gran
```

### Starting the nodes

1. Run the Account 1's node by (Again, `--ws-port 9945` argument is not used because it has been deprecated. Also removed the telemetry argument because it's unnecessary for the demonstration.);

```
  ./target/release/node-template \
  --base-path /tmp/node01 \
  --chain ./customSpecRaw.json \
  --port 30333 \
  --rpc-port 9933 \
  --validator \
  --rpc-methods Unsafe \
  --name Node01 \
  --password 123456
```

2. Run the Account 2's node by (Replaced the local node identity in the `bootnodes` argument with the one that's been outputted when run the first node);

```
  ./target/release/node-template \
  --base-path /tmp/node02 \
  --chain ./customSpecRaw.json \
  --port 30333 \
  --rpc-port 9933 \
  --validator \
  --rpc-methods Unsafe \
  --name Node02 \
  --bootnodes /ip4/127.0.0.1/tcp/30333/p2p/12D3KooWS7v1tPw4b3isyRKxPsx3D4em9n2AwcFiAKb3M8TXPAwp\
  --password qwerty
```

After running the both nodes, I observed that they discovered each other and started generating blocks.

```
💤 Idle (0 peers), best: #0 (0x7bac…ffcf), finalized #0 (0x7bac…ffcf), ⬇ 0 ⬆ 0  
✨ Imported #1 (0x46ac…cdc7)  
💤 Idle (1 peers), best: #1 (0x46ac…cdc7), finalized #0 (0x7bac…ffcf), ⬇ 0.9kiB/s ⬆ 0.8kiB/s  
💤 Idle (1 peers), best: #1 (0x46ac…cdc7), finalized #0 (0x7bac…ffcf), ⬇ 0.5kiB/s ⬆ 0.5kiB/s  
🙌 Starting consensus session on top of parent 0x46aca0410d83897ad59f2dca384f0cd76509a8531c8d354f7e6e3c0b0ff2cdc7  
🎁 Prepared block for proposing at 2 (1 ms) [hash: 0xe3aaa85fbae700fd7af4489c857cd59ccee822994fecb012930b30763e838edf; parent_hash: 0x46ac…cdc7; extrinsics (1): [0x8541…3c90]  
🔖 Pre-sealed block for proposal at 2. Hash now 0xb8f748fab1c57c20fb2bb51a5ca9d5f496ff37cae9e2e0324dd4c9a4426d0f60, previously 0xe3aaa85fbae700fd7af4489c857cd59ccee822994fecb012930b30763e838edf.  
✨ Imported #2 (0xb8f7…0f60)  
💤 Idle (1 peers), best: #2 (0xb8f7…0f60), finalized #0 (0x7bac…ffcf), ⬇ 0.5kiB/s ⬆ 0.6kiB/s
```

## 8. Smart contracts

### Preparing the environment

1. Skipped installing wasm as I installed it in the first step. But I tried the update rust but it was already at the last version.
2. Installed the `cargo-contract` package. The latest version was 3.2.0, so I changed it in the code

   ```
   cargo install --force --locked cargo-contract --version 3.2.0
   ```
3. Downloaded the `substrate-contracts-node` binaries in another folder from github and extracted it using;

   ```
   wget https://github.com/paritytech/substrate-contracts-node/releases/download/v0.30.0/substrate-contracts-node-linux.tar.gz &&\
   mkdir substrate-contracts-node &&\
   tar -xvf substrate-contracts-node-linux.tar.gz -C substrate-contracts-node &&\
   cd substrate-contracts-node/artifacts/substrate-contracts-node-linux &&\
   chmod +x substrate-contracts-node
   ```

### Creating the smart contract

4. In another terminal, run `cargo contract new flipper` to create a new contract project folder called flipper [17e67c2](https://github.com/wintergew/substrate-node-template/commit/17e67c27cfdb91ddd89ad7ca83d47c0f6f875358).
5. Test if the contract compiles with `cargo test` and compile the contract using `cargo contract build`. This creates 3 artifacts;

- flipper.contract (code + metadata)
- flipper.wasm (the contract's code)
- flipper.json (the contract's metadata)

### Deploying the smart contract

6. Run the `substrate-contracts-node` with `./substrate-contracts-node --log info,runtime::contracts=debug 2>&1` so it outputs the debug information.
7. In the flipper folder instantiate the smart contract using (added the `-x` and  `--skip-confirm` arguments);

   ```
   cargo contract instantiate -x --skip-confirm --constructor new --args "false" --suri //Alice --salt $(date +%s)
   ```

   which outputs

   ```
    Dry-running new (skip with --skip-dry-run)
       Success! Gas required estimated at Weight(ref_time: 276548162, proof_size: 16689)
         Events
          Event Balances ➜ Withdraw
            who: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
            amount: 124.425706μUNIT
          Event System ➜ NewAccount
            account: 5F8LUz7pRdkHbQM2CNVWwEqeDaqasfXYaZvd5xzmiXPjneAd
          Event Balances ➜ Endowed
            account: 5F8LUz7pRdkHbQM2CNVWwEqeDaqasfXYaZvd5xzmiXPjneAd
            free_balance: 100.765mUNIT
          Event Balances ➜ Transfer
            from: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
            to: 5F8LUz7pRdkHbQM2CNVWwEqeDaqasfXYaZvd5xzmiXPjneAd
            amount: 100.765mUNIT
          Event System ➜ NewAccount
            account: 5Fky7RVZ4RJvHXDg7LeuyZqZRRbzvTazBFkZ6Wg4cnq7ZbAC
          Event Balances ➜ Endowed
            account: 5Fky7RVZ4RJvHXDg7LeuyZqZRRbzvTazBFkZ6Wg4cnq7ZbAC
            free_balance: 1mUNIT
          Event Balances ➜ Transfer
            from: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
            to: 5Fky7RVZ4RJvHXDg7LeuyZqZRRbzvTazBFkZ6Wg4cnq7ZbAC
            amount: 1mUNIT
          Event Contracts ➜ Instantiated
            deployer: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
            contract: 5Fky7RVZ4RJvHXDg7LeuyZqZRRbzvTazBFkZ6Wg4cnq7ZbAC
          Event Balances ➜ Transfer
            from: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
            to: 5F8LUz7pRdkHbQM2CNVWwEqeDaqasfXYaZvd5xzmiXPjneAd
            amount: 100.005mUNIT
          Event TransactionPayment ➜ TransactionFeePaid
            who: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
            actual_fee: 124.425706μUNIT
            tip: 0UNIT
          Event System ➜ ExtrinsicSuccess
            dispatch_info: DispatchInfo { weight: Weight { ref_time: 3500476303, proof_size: 9095 }, class: Normal, pays_fee: Yes }

       Contract 5Fky7RVZ4RJvHXDg7LeuyZqZRRbzvTazBFkZ6Wg4cnq7ZbAC


   ```

   and the `substrate-contracts-node` terminal outputs

   ```
   INFO tokio-runtime-worker jsonrpsee_server::server: Accepting new connection 1/100
   INFO tokio-runtime-worker jsonrpsee_server::server: Accepting new connection 2/100
   ```

### Interacting with the smart contract

8. Trying the `get()` function in the smart contract. Replaced the contract adress with the generated adress.

   ```
   cargo contract call --contract 5Fky7RVZ4RJvHXDg7LeuyZqZRRbzvTazBFkZ6Wg4cnq7ZbAC --message get --suri //Alice --dry-run

   ```
   which outputs

   ```
   Result Success!
   Reverted false
    Data Ok(false)
   Your message call has not been executed.
   To submit the transaction and execute the call on chain, add -x/--execute flag to the command.
   ```
9. Trying the `flip()` function in the smart contract.

   ```
   cargo contract call --contract 5Fky7RVZ4RJvHXDg7LeuyZqZRRbzvTazBFkZ6Wg4cnq7ZbAC --message flip --suri //Alice -x --skip-confirm
   ```
   which outputs

   ```
    Dry-running flip (skip with --skip-dry-run)
       Success! Gas required estimated at Weight(ref_time: 784946611, proof_size: 28563)
         Events
          Event Balances ➜ Withdraw
            who: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
            amount: 124.414154μUNIT
          Event Contracts ➜ Called
            caller: Signed(5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY)
            contract: 5Fky7RVZ4RJvHXDg7LeuyZqZRRbzvTazBFkZ6Wg4cnq7ZbAC
          Event TransactionPayment ➜ TransactionFeePaid
            who: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
            actual_fee: 124.414154μUNIT
            tip: 0UNIT
          Event System ➜ ExtrinsicSuccess
            dispatch_info: DispatchInfo { weight: Weight { ref_time: 1726603611, proof_size: 18985 }, class: Normal, pays_fee: Yes }
   ```
