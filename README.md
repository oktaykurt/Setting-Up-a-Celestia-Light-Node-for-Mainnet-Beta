# Setting Up a Celestia Light Node for Mainnet Beta

This guide will walk you through setting up a Celestia light node on the Mainnet Beta, enabling you to perform data availability sampling (DAS) on Celestia's data availability (DA) network.

![Celestia Light Node](https://docs.celestia.org/img/nodes/LightNodes.png)

## Overview of Light Nodes

Light nodes in Celestia perform critical functions to ensure data availability:
- **Listening for ExtendedHeaders**: These are wrapped “raw” headers that notify Celestia nodes of new block headers and relevant DA metadata.
- **Performing DAS**: They sample the received headers for data availability.

## Hardware Requirements

To run a light node, the following minimum hardware requirements are recommended:
- **Memory**: 500 MB RAM
- **CPU**: Single Core
- **Disk**: 100 GB SSD Storage
- **Bandwidth**: 56 Kbps for Download/Upload

## Setting Up Your Light Node

This setup is based on an Ubuntu Linux 20.04 (LTS) x64 instance.

### Step 1: System Update and Dependency Installation

1. **Update the System**:
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```

2. **Install Essential Packages**:
    ```bash
    sudo apt install curl tar wget aria2 clang pkg-config libssl-dev jq build-essential git make ncdu -y
    ```

3. **Install Golang**:
    ```bash
    ver="1.22.0"
    cd $HOME
    wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
    sudo rm -rf /usr/local/go
    sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
    rm "go$ver.linux-amd64.tar.gz"
    echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
    source $HOME/.bash_profile
    go version
    ```

### Step 2: Install Celestia Node

1. **Remove Existing Copies and Clone the Repository**:
    ```bash
    cd $HOME
    rm -rf celestia-node
    git clone https://github.com/celestiaorg/celestia-node.git
    cd celestia-node/
    ```

2. **Checkout the Desired Version**:
    ```bash
    git checkout tags/v0.14.1
    ```

3. **Build the Celestia Binary**:
    ```bash
    make build
    ```

4. **Install the Binary**:
    ```bash
    sudo make install
    ```

5. **Build the `cel-key` Utility**:
    ```bash
    make cel-key
    ```

6. **Verify Installation**:
    ```bash
    celestia version
    ```

### Step 3: Running Celestia Node in a Screen Session

To ensure your Celestia node runs uninterrupted, you can use the `screen` utility.

1. **Install Screen**:
    ```bash
    sudo apt install screen
    ```

2. **Create a New Screen Session**:
    ```bash
    screen -S celestia
    ```

### Step 4: Initialize or Recover the Light Node

At this point, you can choose to either initialize a new light node or recover an existing one using your mnemonic phrase. Perform these steps within the screen session.

#### Option A: Initialize a New Light Node

1. **Initialize the Light Node**:
    ```bash
    celestia light init
    ```

2. **Start the Light Node**:
    ```bash
    celestia light start --core.ip rpc.celestia.pops.one --p2p.network celestia
    ```
#### Option B: Recover an Existing Key from Mnemonic

1. **Recover Key from Mnemonic**:
    ```bash
    ./cel-key add my_celes_key --recover --keyring-backend test --node.type light --p2p.network celestia
    ```

2. **List the Existing Keys**:
    ```bash
    ./cel-key list --node.type light --keyring-backend test --p2p.network celestia
    ```

3. **Start the Light Node with the Recovered Key**:
    ```bash
    celestia light start --keyring.accname my_celes_key --core.ip rpc.celestia.pops.one --p2p.network celestia
    ```

### Step 5: Detach and Manage the Screen Session

1. **Detach from the Screen Session**:
    Press `Ctrl + A`, then `D` to detach from the screen session without stopping the node.

2. **Reattach to the Screen Session**:
    To reattach to the screen session later, use:
    ```bash
    screen -r celestia
    ```

3. **Kill the Screen Session**:
    To stop the screen session, use:
    ```bash
    screen -X -S celestia quit
    ```
For more detailed information, refer to the [Celestia Documentation](https://docs.celestia.org/nodes/light-node) and the [Celestia GitHub repository](https://github.com/celestiaorg/docs/blob/main/nodes/light-node.md).    
