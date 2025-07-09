# Sensōra: A Decentralized Peer-to-Peer IoT Data Marketplace

<p align="center">
  <img src="https://raw.githubusercontent.com/msmrez/sensora/main/logo.png" alt="Sensōra Logo" width="200"/>
</p>

<p align="center">
  <strong>Empowering every IoT device to become an autonomous economic agent on the BSV blockchain.</strong>
  <br />
  <a href="#the-vision"><strong>The Vision</strong></a> ·
  <a href="#how-it-works"><strong>How It Works</strong></a> ·
  <a href="#core-features"><strong>Features</strong></a> ·
  <a href="#getting-started"><strong>Getting Started</strong></a> ·
  <a href="#repositories"><strong>Repositories</strong></a>
</p>

---

## The Vision

In today's world, IoT data is siloed. It's collected by centralized platforms that act as middlemen, controlling access, taking a cut of the profits, and creating a single point of failure and control.

**Sensōra changes this.**

Sensōra is an open-source protocol and a suite of tools that creates a truly peer-to-peer marketplace for IoT data. It enables any sensor—from a simple temperature sensor on a Raspberry Pi to a complex industrial machine—to sell its data directly to consumers for BSV micropayments. There are no intermediaries, no platform fees, and no central servers controlling the data.

Data integrity is guaranteed through on-chain cryptographic proofs, and agent reliability is measured and published through a decentralized reputation system.

**[Link to Live Indexer Dashboard Demo]** `(<- Optional: Add a link if you host a public indexer)`

## How It Works: The Sensōra Ecosystem

The network consists of three independent but interconnected components that communicate via the BSV blockchain.

```
┌─────────────────┐   1. Advertise Service   ┌────────────────┐   4. Discover Agent   ┌──────────────┐
│  Sensōra Agent  │  ─────────────────────>  │                │  <───────────────────  │              │
│ (Provider)      │                          │ BSV Blockchain │                        │ Sensōra      │
│ on IoT Device   │   2. Stamp Data Proof    │                │   5. Purchase Data     │ Client       │
│                 │  <─────────────────────  │ (Ledger)       │  ───────────────────>  │ (Consumer)   │
└─────────────────┘   3. Listen for Ads      └────────────────┘   6. Verify Proof      └──────────────┘
       ^            <─────────────────────                          ^
       │                                  │                         │
       │ 7. Query for Agents              │ 3a. Index results       │
       │                                  │                         │
       └───────────────────────────────────[ Sensōra Indexer ]───────┘
                                                (Registry)
```

1.  **[Sensōra Agent](https://github.com/msmrez/sensora_agent):** A lightweight Python application that runs on an IoT device. It periodically stamps data proofs on-chain and serves its data for a price.
2.  **[Sensōra Indexer](https://github.com/msmrez/sensora_indexer):** A backend service that listens to the blockchain, discovers active agents, audits their reputation, and provides a public API and dashboard for discovery.
3.  **[Sensōra Client](https://github.com/msmrez/sensora_client):** A command-line tool that demonstrates the full consumer lifecycle: discovering the best agent via the indexer, purchasing data in single or batch mode, and cryptographically verifying its integrity.

## Core Features

-   **Decentralized & Peer-to-Peer:** Agents sell data directly to consumers. No central platform or middleman is required for transactions.
-   **On-Chain Data Integrity:** Every sensor reading is anchored to the BSV blockchain with a `SENSORA_PROOF` transaction, creating an immutable, timestamped proof of the data's existence and authenticity.
-   **On-Chain Discovery:** Agents advertise their services (data types, price, location) on-chain using the `SENSORA_ADV` protocol, allowing indexers to build a censorship-resistant registry.
-   **Reputation System:** The indexer acts as a public auditor, periodically checking agent uptime and performance. Consumers can use this reputation data to select the most reliable providers.
-   **Direct Micropayments:** Data is purchased directly from the agent for BSV micropayments, enabling a true data economy at scale.
-   **Batch Buying:** The protocol supports efficient purchasing of large historical datasets from an agent in a single, verified transaction.
-   **Open Source:** All components are fully open-source under the MIT license, allowing anyone to run an agent, an indexer, or build a new client application.
-   **Key Technologies:** Built on BSV Blockchain, IPv6 for true P2P addressing, and Python 3.

## Getting Started: Running the Full Ecosystem

To run a complete, local Sensōra network, you will need to set up all three components. It is recommended to use three separate terminal windows.

### Prerequisites
- Python 3.10+
- A Linux environment (e.g., a cloud VM or local machine)
- A funded BSV wallet to provide a WIF (private key) for the Agent and Client.

### Step 1: Set up the Indexer & Registry
The indexer is the discovery layer of the network. It needs to be running first to discover the agent.

```bash
# Clone the repository
git clone https://github.com/msmrez/sensora_indexer.git
cd sensora_indexer

# Set up environment and install
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
pip install -e .

# Run the Indexer Daemon (in Terminal 1)
python -m sensora_indexer.daemon

# Run the Indexer API (in Terminal 2)
python -m sensora_indexer.api
```
The indexer dashboard will now be running, typically at `http://YOUR_IP:8081`.

### Step 2: Set up the Agent
The agent is the data provider that will be discovered by the indexer.

```bash
# In a new directory, clone the repository
git clone https://github.com/msmrez/sensora_agent.git
cd sensora_agent

# Set up environment and install
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# Configure the agent
cp config.ini.example config.ini
# Edit config.ini with your device's IPv6, a port (e.g., 8083), and your funded WIF.

# Run the Agent (in Terminal 3)
python app.py
```
After a few minutes, the agent will broadcast an advertisement transaction, and it should appear on the indexer's dashboard.

### Step 3: Use the Client to Buy Data
The client uses the indexer to find the agent and purchase its data.

```bash
# In a new directory, clone the repository
git clone https://github.com/msmrez/sensora_client.git
cd sensora_client

# Set up environment and install
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# Configure the client
cp src/sensora_client/config.py.example src/sensora_client/config.py
# Edit src/sensora_client/config.py and set REGISTRY_API_URL to your indexer's URL.

# Run the Client to buy data
python -m sensora_client.client "YOUR_CONSUMER_WIF_HERE"
```
The client will now automatically discover your running agent, purchase data, and verify its integrity on-chain.

## Repositories

This project is composed of three main repositories:

-   **[sensora_agent](https://github.com/msmrez/sensora_agent):** The agent software that runs on any IoT device to sell data.
-   **[sensora_indexer](https://github.com/msmrez/sensora_indexer):** The network service that discovers and catalogs agents, providing a public API.
-   **[sensora_client](https://github.com/msmrez/sensora_client):** A reference client and utility for discovering, purchasing, and verifying data.

## Contributing

This project is in active development. We welcome feedback, bug reports, and pull requests. Please feel free to open an issue or submit a PR in the relevant repository.

## License

This project is licensed under the MIT License - see the LICENSE file for details.
