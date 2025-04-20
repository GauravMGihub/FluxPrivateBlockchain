# FluxPrivateBlockchain
# Project: Creation of Cryptocurrency Infrastructure using a Private Blockchain

## Overview

This project documents the process of setting up a private Ethereum blockchain network using Go Ethereum (Geth) for the purpose of creating a controlled environment to deploy and test smart contracts. While titled "Creation of cryptocurrency," the core focus was on building the foundational private blockchain infrastructure, connecting development tools, managing accounts, deploying a basic smart contract, and troubleshooting common issues encountered during this process. The ultimate goal was to establish a functional local testbed simulating the Ethereum network without incurring real-world costs or delays.

## Technologies Used

*   **Go Ethereum (Geth):** Official command-line client for running an Ethereum node. Used to create and manage the private blockchain.
*   **MetaMask:** Browser extension wallet used for account management and interacting with the blockchain via dApps/IDEs.
*   **Remix IDE:** Web-based Integrated Development Environment for writing, compiling, and deploying Solidity smart contracts.
*   **Solidity:** Programming language used for writing the smart contract (`SimpleStorage.sol`).
*   **MyEtherWallet (MEW) Offline:** Used securely offline to extract a private key from a Geth keystore file when MetaMask import failed.
*   **Command Line Interface (CLI):** For running Geth commands and potentially other utilities.
*   **Text Editor:** For managing password files and temporary key storage.
*   **Web Browser:** (e.g., Chrome, Firefox) For accessing Remix IDE and running the MetaMask extension.

## Setup and Implementation Steps

1.  **Prerequisites:**
    *   Installed Go Ethereum (Geth).
    *   Installed MetaMask browser extension.

2.  **Geth Node Initialization & Configuration:**
    *   Defined configurations for potentially two nodes. Node 1 was the primary node used for mining and RPC interaction.
    *   **Node 1 Startup Command:**
        ```bash
        geth --networkid 14333 --datadir "./data" --bootnodes "enode://1b5ebf3079d49728f34a1307ccd81af0468aa133fb9625043ef7a27b6127fdccc4674fb6910935feae9a8c36504bb4429c26175c96518cdb70e97b5fb8941ada@127.0.0.1:30301" --port 30303 --ipcdisable --syncmode full --http --http.corsdomain "*" --http.port 8545 --http.api "eth,net,web3,personal,miner" --allow-insecure-unlock --unlock "0xe3CCDa925F0C062047dA051BEA1fAE6cA115E5dd" --password password.txt --mine console
        ```
        *   `--networkid 14333`: Sets a unique ID for the private network.
        *   `--datadir "./data"`: Specifies the data storage location.
        *   `--http --http.port 8545`: Enables the HTTP-RPC server on port 8545.
        *   `--http.corsdomain "*"`: Allows connections from browser-based tools like MetaMask/Remix.
        *   `--http.api "..."`: Specifies enabled RPC modules.
        *   `--unlock "0xe3CC..."`: Unlocks the specified account using the password from `password.txt`. This account will receive mining rewards.
        *   `--mine`: Starts the mining process.
        *   `console`: Attaches an interactive JavaScript console.
    *   Node 1 was observed to successfully start and begin mining blocks.

3.  **MetaMask Network Configuration:**
    *   Added a custom network in MetaMask:
        *   Network Name: `My Local Geth 14333` (or similar)
        *   New RPC URL: `http://127.0.0.1:8545`
        *   Chain ID: `14333`
        *   Currency Symbol: `ETH`

4.  **Account Management (Importing Miner Account):**
    *   The primary funded account was the one unlocked during Geth startup (`0xe3CC...`).
    *   Attempted to import this account into MetaMask using its Geth Keystore JSON file (`./data/keystore/...`).
    *   **Encountered Issue:** MetaMask froze during JSON import (See Troubleshooting).
    *   **Solution:** Successfully imported the account using its Private Key extracted securely via MEW Offline.

5.  **Smart Contract Preparation:**
    *   Used Remix IDE (`remix.ethereum.org`).
    *   Created a basic `SimpleStorage.sol` contract:
        ```solidity
        // SPDX-License-Identifier: MIT
        pragma solidity ^0.8.10;

        contract SimpleStorage {
            uint storedData;

            function set(uint x) public {
                storedData = x;
            }

            function get() public view returns (uint) {
                return storedData;
            }
        }
        ```
    *   Compiled the contract successfully in Remix.

6.  **Connecting Remix and Deploying:**
    *   In Remix's "Deploy & Run Transactions" tab, selected `Injected Provider - MetaMask` as the environment.
    *   Authorized the connection in MetaMask, ensuring the imported miner account (`0xe3CC...`) was selected.
    *   Clicked "Deploy".

7.  **Deployment Transaction Handling:**
    *   MetaMask popped up for confirmation.
    *   **Encountered Issue:** "Gas estimation failed" (See Troubleshooting).
    *   **Encountered Issue:** Incorrect network fee currency ("FLUX") displayed (See Troubleshooting).
    *   Successfully submitted the transaction after resolving issues, confirming with the correct network and non-zero gas fee (paid by the miner account).

## Troubleshooting / Challenges Encountered

This project involved overcoming several common hurdles:

1.  **MetaMask JSON Import Freeze:**
    *   **Symptom:** When trying to import the Geth account using the Keystore JSON file, the MetaMask interface showed a loading message ("Expect this JSON import...") and became unresponsive indefinitely.
    *   **Solution:** Used the **Private Key Import** method as a workaround:
        1.  Downloaded **MEW Offline** (`MEW v6.9.2.offline.zip`).
        2.  **Disconnected** the computer from the internet.
        3.  Extracted and ran `MEW Offline` locally (`index.html`).
        4.  Accessed the wallet using the **Keystore File** option, providing the correct Geth Keystore JSON file and password (`password.txt`).
        5.  Revealed and securely **copied the Private Key**.
        6.  **Reconnected** to the internet.
        7.  Opened MetaMask (required a browser restart, see next issue).
        8.  Used MetaMask's **"Import account" -> "Private Key"** option, pasting the key.
        9.  Securely deleted all temporary traces of the private key.

2.  **MetaMask UI Not Loading:**
    *   **Symptom:** After going offline for MEW and reconnecting, the MetaMask extension UI would not load when clicked.
    *   **Solution:** **Restarting the web browser** completely resolved this loading issue.

3.  **Remix Deployment "Gas Estimation Failed":**
    *   **Symptom:** When clicking "Deploy" in Remix, the MetaMask confirmation pop-up showed "Gas estimation failed".
    *   **Troubleshooting:** Verified that the correct account (`0xe3CC...`) was selected in MetaMask and displayed a non-zero balance for the correct local network (`14333`). Ensured the Geth node was running.
    *   **Solution:** Since the account had funds and the network was local/fast, clicked the **"Send Transaction"** option in MetaMask, bypassing the estimation.

4.  **Incorrect Network Fee Currency ("FLUX"):**
    *   **Symptom:** After bypassing gas estimation, the MetaMask confirmation showed the network fee denominated in "FLUX" instead of the expected "ETH".
    *   **Troubleshooting:** Indicated that MetaMask was inadvertently connected to or attempting to transact on the wrong network (likely Flux Testnet).
    *   **Solution:**
        1.  **Rejected** the transaction in MetaMask.
        2.  Explicitly **selected the correct local Geth network** (`My Local Geth 14333` / Chain ID `14333`) from the MetaMask network dropdown.
        3.  Refreshed Remix and re-initiated the deployment.
        4.  Verified the subsequent MetaMask pop-up showed fees in **ETH**.

## Results

*   A private Ethereum blockchain network (Network ID `14333`) was successfully established using Geth.
*   Node 1 successfully operated as a miner, generating blocks and test ETH for the unlocked account (`0xe3CC...`).
*   MetaMask was successfully configured to connect to the local Geth node's RPC endpoint.
*   The Geth miner account was successfully imported into MetaMask, overcoming JSON import issues via secure private key extraction.
*   A standard `SimpleStorage` smart contract was compiled in Remix IDE.
*   The `SimpleStorage` contract was successfully deployed onto the private blockchain network after resolving gas estimation and network selection issues.
*   Deployment confirmation was observed in both Remix IDE (deployed contract address appeared) and the Geth node's console logs (contract creation transaction included in a mined block).
*   The project resulted in a functional local development and testing environment for Ethereum smart contracts.

## Future Work

*   Deploy and test more complex smart contracts.
*   Configure and compare different consensus mechanisms (e.g., Clique PoA).
*   Set up and test interactions between multiple Geth nodes.
*   Integrate a private block explorer (e.g., Blockscout).
*   Develop a simple dApp frontend (e.g., using ethers.js) to interact with contracts on this private network.

## Acknowledgements

Initial guidance for setting up the Geth nodes and bootnode connections was referenced from the YouTube playlist: [https://youtube.com/playlist?list=PLkM0MH7Grb25poKEiId5pEQg-OzLQRNM4&si=jUhZsCHBGM0qdqbb](https://youtube.com/playlist?list=PLkM0MH7Grb25poKEiId5pEQg-OzLQRNM4&si=jUhZsCHBGM0qdqbb)
