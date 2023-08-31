# Polkadot Substrate Development Final Project
The following are the steps I've been taken during the making of my final project.
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

## 8. Smart contracts