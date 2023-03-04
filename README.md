# `CrossChain NFT`

> Effortlessly transfer NFT's from one chain to another. Made using Router Cross-Talk.

🚀DEMO: [Link to be given]

This project is built with [Router CrossTalk](https://dev.routerprotocol.com/crosstalk-library/overview/introduction)

Router Protocol is a solution introduced to address the issues hindering the usability of cross-chain liquidity migration in the DeFi ecosystem. It acts as a bridge connecting various layer 1 and layer 2 blockchains, allowing for the flow of contract-level data across them. The Router Protocol can either transfer tokens between chains or initiate operations on one chain and execute them on another.

Please check the [official documentation of Router Protocol](https://www.routerprotocol.com/) 

![CrossChain NFT](Demo gif to placed here)

# ⭐️ `Star us`

If this repository helps you build cross-chain dapps faster and easier - please star this project, every star makes us very happy!

# 🤝 `Need help?`

If you need help or have other some questions - don't hesitate to write in our discord channel and we will check asap. [Discord link](https://discord.gg/xvx2pFu9). The best thing about this is the super active community ready to help at any time! We help each other.

# 🚀 `Quick Start`

📄 Clone or fork `CrossChain NFT`:

```sh
git clone https://github.com/protocol-designer/CrossChain-NFT.git
```

💿 Install all dependencies:

```sh
cd cross-chat-main
npm install
```

🚴‍♂️ Run your App:

```sh
npm start
```
# 🧭 `Table of contents`
- [🚀 Quick Start](#-quick-start)
- [🧭 Table of contents](#-table-of-contents)
- [🏗 Backend](#Solidity, Router Cross-Talk Library)
  - [`Initiating the Contract`](#Initiating-the-Contract)
  - [`Creating state variables and the constructor`](#Creating-state-variables-and-the-constructor)
  - [`Setting up the Destination Contract on the Source Contract`](#Setting-up-the-Destination-Contract-on-the-Source-Contract)
  - [`Transferring an NFT from a source chain to a destination chain`](#Transferring-an-NFT-from-a-source-chain-to-a-destination-chain)
  - [`Handling a cross-chain request`](#Handling-a-cross-chain-request)
  - [`Handling the acknowledgement received from destination chain`](#Handling-the-acknowledgement-received-from-destination-chain)


  
# 🏗 Frontend


# 🏗 Backend
  
## `Initiating the Contract`

For initiating the smart contract named "CrossChainNFT", the contract imports four external contracts :-

1. **ICrossTalkApplication.sol**

2. **Utils.sol**

3. **IGateway.sol**

4. **ERC1155.sol**

The "ICrossTalkApplication.sol", "Utils.sol" and "IGateway.sol" contracts are imported from the "evm-gateway-contract/contracts" and "ERC1155.sol" from "openzeppelin/contracts/token".The "CrossChain" contract implements the "ICrossTalkApplication" and "ERC1155.sol" contract by inheriting from them. This means that the "CrossChainNFT" contract must have all the functions and variables defined in the "ICrossTalkApplication" contract. By importing and implementing these contracts, the "CrossChainNFT" contract will have access to their functionality and will be compatible with other contracts that follow the same standards.

```sh
//SPDX-License-Identifier: UNLICENSED
pragma solidity >=0.8.0 <0.9.0;

import "evm-gateway-contract/contracts/ICrossTalkApplication.sol";
import "evm-gateway-contract/contracts/Utils.sol";
import "evm-gateway-contract/contracts/contracts/CrossTalkUtils.sol";
import "@openzeppelin/contracts/token/ERC1155/ERC1155.sol";

contract CrossChainNFT is ERC1155, ICrossTalkApplication {
}
```
## `Creating State Variables and the Constructor`

The smart contract has the following state variables:

1. **admin** - an address variable which stores the address of the admin. This will be used for access control purposes.

2. **gatewayContract** - an address variable which holds the address of the gateway contract. This contract will route messages to the Router Chain.

3. **destGasLimit** - a uint64 variable which indicates the amount of gas required to execute the function that will handle cross-chain requests on the destination chain.

4. **ourContractOnChains** - a mapping which maps a chain type and chain ID to the address of NFT contracts deployed on different chains. This mapping will be used to set the address of the destination contract.

The smart contract also defines a struct TransferParams which includes the following parameters:

1. **nftIds** - an array of uint256 values which represent the IDs of the NFTs to be transferred.

2. **nftAmounts** -  an array of amounts of the respective NFT Ids to be transferred to the recipient on the destination chain..

3. **nftData** - a bytes variable which holds additional data related to the NFT transfer.You can send 0x00 if you don’t want to send any data while minting the NFT.

4. **recipient** - a bytes variable which holds the address of the recipient of the NFT transfer.

The constructor of the smart contract takes three parameters:

1. **uri** - a string which represents the URI for the NFTs being created.

2. **gatewayAddress** - an address variable which holds the address of the gateway contract.

3. **_destGasLimit** - a uint64 variable which indicates the amount of gas required to execute the function that will handle cross-chain requests on the destination chain.

The smart contract extends the ERC1155 standard and includes all the required functions such as balanceOf, safeTransferFrom, setApprovalForAll, and others.

```sh
address public admin;
address public gatewayContract;
uint64 public destGasLimit;
// chain type + chain id => address of our contract in bytes
mapping(uint64 => mapping(string => bytes)) public ourContractOnChains;

struct TransferParams {
    uint256[] nftIds;
    uint256[] nftAmounts;
    bytes nftData;
    bytes recipient;
}

constructor(
    string memory uri,
    address payable gatewayAddress, 
    uint64 _destGasLimit
) ERC1155(uri) {
    gatewayContract = gatewayAddress;
    destGasLimit = _destGasLimit;
    admin = msg.sender;
}
```

## `Setting up the Destination Contract on the Source Contract`

**setContractOnChain**:-

The given code defines a setter function setContractOnChain which allows the admin to set the address of a destination contract on the source chain and visa versa. The function takes three parameters:

chainType - a uint64 variable which represents the type of the chain (e.g. Ethereum, Binance Smart Chain, etc.)
chainId - a string which represents the ID of the chain where the contract is deployed.
contractAddress - an address variable which holds the address of the NFT contract deployed on the specified chain.
The function first checks whether the caller is the admin by comparing the msg.sender with the admin address variable. If the caller is not the admin, the function will revert with an error message "only admin".

If the caller is the admin, the function will convert the contractAddress to bytes using the toBytes function and store it in the ourContractOnChains mapping using the chainType and chainId as the keys.

```sh
function setContractOnChain(
    uint64 chainType, 
    string memory chainId, 
    address contractAddress
) external {
    require(msg.sender == admin, "only admin");
    ourContractOnChains[chainType][chainId] = toBytes(contractAddress);
}
```
## `Transferring an NFT from a source chain to a destination chain`

**transferCrossChain:-**

This function allows a user to transfer their NFTs from their account on one chain to their account on a destination chain. The function burns the specified NFTs from the user's account, creates a cross-chain communication request to the destination chain, and passes the transfer parameters as payload in the request.

The function accepts the following parameters:

1. **chainType**: A uint64 representing the type of the destination chain. See the link provided in the description for the list of available chain types.

2. **chainId**: A string representing the ID of the destination chain.

3. **expiryDurationInSeconds**: A uint64 representing the duration in seconds for which the cross-chain request created after calling this function remains valid. If the expiry duration elapses before the request is executed on the destination chain contract, the request will fail.

4. **destGasPrice**: A uint64 representing the gas price of the destination chain.

5. **transferParams**: A struct of type TransferParams which receives the NFT Ids and the respective amounts the user wants to transfer to the destination chain. It also receives the arbitrary data to be used while minting the NFT on the destination chain and the address of recipient in bytes.

The function burns the specified NFTs from the user's account and creates a cross-chain communication request to the destination chain. The payload for the request is generated by ABI encoding the transferParams. The expiry timestamp for the request is calculated as block.timestamp + expiryDurationInSeconds.

The function uses the CrossTalkUtils library to generate the cross-chain communication request to the destination chain. The function also uses the ourContractOnChains mapping to retrieve the address of the NFT contract on the destination chain.

```sh
function transferCrossChain(
    uint64 chainType,
    string memory chainId,
    uint64 expiryDurationInSeconds,
    uint64 destGasPrice,
    TransferParams memory transferParams
  ) public payable {
        // burning the NFTs from the address of the user calling this function
    _burnBatch(msg.sender, transferParams.nftIds, transferParams.nftAmounts);

    bytes memory payload = abi.encode(transferParams);
    uint64 expiryTimestamp = uint64(block.timestamp) + expiryDurationInSeconds;
    Utils.DestinationChainParams memory destChainParams = 
                    Utils.DestinationChainParams(
                        destGasLimit,
                        destGasPrice,
                        chainType,
                        chainId
                    );      

    CrossTalkUtils.singleRequestWithoutAcknowledgement(
        gatewayContract,
        expiryTimestamp,
        destChainParams,
        ourContractOnChains[chainType][chainId],  // destination contract address
        payload
    );
  }
  ```
 
## `Handling a cross-chain request`

**handleRequestFromSource function:-**

This function is called by the Gateway contract on the destination chain, which is triggered when a cross-chain transfer request is sent by our contract on the source chain. This function receives several parameters, including the source contract address, the payload, the source chain ID, and the source chain type.

1. **srcContractAddress**: A bytes array that represents the address of the contract on the source chain that initiated the cross-chain transfer request.

2. **payload**: A bytes array that contains information about the NFTs that are being transferred, including the recipient's address, NFT IDs, amounts, and additional data.

3. **srcChainId**: A string that represents the ID of the source chain from which the cross-chain transfer request originated.

4. **srcChainType**: An unsigned 64-bit integer that represents the type of the source chain from which the cross-chain transfer request originated.

he function first checks that the call is made only by the Gateway contract and that the request is received from our contract on the source chain. If the conditions are not met, the function will revert the transaction.

The payload that was sent with the cross-chain transfer request contains information about the NFTs that are being transferred, such as the recipient's address, the NFT IDs, the amounts, and any additional data. The function decodes the payload into a TransferParams struct.

After decoding the payload, the function uses the _mintBatch function of the ERC-1155 contract from the OpenZeppelin library to mint the NFTs to the recipient on the destination chain. The function converts the address of the recipient from bytes to an address format using the toAddress function provided in the CrossTalkUtils library.

Finally, the function returns the source chain ID and source chain type in encoded bytes.

```sh
function handleRequestFromSource(
  bytes memory srcContractAddress,
  bytes memory payload,
  string memory srcChainId,
  uint64 srcChainType
) external override returns (bytes memory) {
  require(msg.sender == address(gatewayContract));
    require(
    keccak256(srcContractAddress) == 
        keccak256(ourContractOnChains[srcChainType][srcChainId])
    );

  TransferParams memory transferParams = abi.decode(payload, (TransferParams));
    _mintBatch(
        CrossTalkUtils.toAddress(transferParams.recipient), 
        transferParams.nftIds, 
        transferParams.nftAmounts, 
        transferParams.nftData
    );

  return abi.encode(srcChainId, srcChainType);
}
```
## `Handling the acknowledgement received from destination chain`

**handleCrossTalkAck:-**

The handleCrossTalkAck function is a public function that needs to be implemented in the contract to satisfy the ICrossTalkApplication interface.
The function takes three parameters: **eventIdentifier**, **execFlags**, and **execData** . However, since we are only implementing an empty function, we do not need to use these parameters.

The handleCrossTalkAck function does not perform any operation on the contract or on the blockchain. It is implemented as an empty function and only serves as a placeholder to satisfy the interface requirements.

Therefore, the handleCrossTalkAck function should be implemented with an empty body as shown in the code provided.

```sh
function handleCrossTalkAck(
  uint64, //eventIdentifier,
  bool[] memory, //execFlags,
  bytes[] memory //execData
) external view override {}
```
