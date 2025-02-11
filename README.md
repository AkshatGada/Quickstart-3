# How to Transfer an Asset and Call a Contract from Agglayer-Connected Chains

This tutorial demonstrates how to perform a combined operation that bridges an asset and calls a contract across Agglayer-connected chains using **lxly.js**. In this example, you will:

- Deploy a Solidity smart contract that can receive an asset and execute a function call.
- Use **lxly.js** to bridge an asset (ETH) and simultaneously call a function on the deployed contract.
- Claim the bridged asset and the associated message on the destination chain.

For this tutorial, we assume the following network configuration:

- **Source Chain:** Cardona (network ID: 1)
- **Destination Chain:** Sepolia (network ID: 0)
- **Asset:** ETH (represented using the zero address)
- **Smart Contract Function:** A function that receives ETH (via `msg.value`) and processes the call (e.g. updating counters)

Make sure you have Node.js installed and your project set up with the necessary dependencies (such as **lxly.js**, your network configuration in `./utils/utils_lxly`, and the proper ABIs).

---

## Step 1. Deploy the Smart Contract

Create a Solidity file named `AssetAndCallReceiver.sol` with the following code. This contract contains the `processTransferAndCall` function which receives ETH (ensuring that the sent value matches the specified asset amount) and updates its state accordingly.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract AssetAndCallReceiver {
    uint256 public totalTransferred;
    uint256 public callCounter;
    
    event AssetReceived(address indexed sender, uint256 amount);
    event CallExecuted(
        address indexed caller,
        uint256 assetAmount,
        uint256 totalTransferred,
        uint256 callCounter
    );

    // This function receives an asset (ETH) and processes a call.
    // The sent ETH (msg.value) must match the specified assetAmount.
    function processTransferAndCall(uint256 assetAmount) external payable {
        require(msg.value == assetAmount, "Asset amount mismatch");

        totalTransferred += assetAmount;
        callCounter++;

        emit AssetReceived(msg.sender, assetAmount);
        emit CallExecuted(msg.sender, assetAmount, totalTransferred, callCounter);
    }
}
```
