# `CrossChain ERC-20`

> Effortlessly transfer ERC-20 tokens from one chain to another. Made using Router Cross-Talk.

🚀DEMO: [Link to be given]

This project is built with [Router CrossTalk](https://dev.routerprotocol.com/crosstalk-library/overview/introduction)

Router Protocol is a solution introduced to address the issues hindering the usability of cross-chain liquidity migration in the DeFi ecosystem. It acts as a bridge connecting various layer 1 and layer 2 blockchains, allowing for the flow of contract-level data across them. The Router Protocol can either transfer tokens between chains or initiate operations on one chain and execute them on another.

Please check the [official documentation of Router Protocol](https://www.routerprotocol.com/) 

![CrossChain ERC-20](Demo gif to placed here)

# ⭐️ `Star us`

If this repository helps you build cross-chain dapps faster and easier - please star this project, every star makes us very happy!

# 🤝 `Need help?`

If you need help or have other some questions - don't hesitate to write in our discord channel and we will check asap. [Discord link](https://discord.gg/xvx2pFu9). The best thing about this is the super active community ready to help at any time! We help each other.

# 🚀 `Quick Start`

📄 Clone or fork `CrossChainERC20`:

```sh
git clone https://github.com/router-resources/ERC20-Cookbook.git
```

✍️ Setting up your editor:

Browse to [Remix IDE](https://remix.ethereum.org/) and create a new file with ".sol" extension.

💿 Install all dependencies:

You don't need to install any dependencies. Remix automatically downloads all the dependencies for you during the time of compile.

# 🧭 `Table of contents`
- [🚀 Quick Start](#-quick-start)
- [🧭 Table of contents](#-table-of-contents)
- [`Initiating the Contract`](#Initiating-the-Contract)
- [`Creating state variables and the constructor`](#Creating-state-variables-and-the-constructor)
- [`Setting up the Destination Contract on the Source Contract`](#Setting-up-the-Destination-Contract-on-the-Source-Contract)
- [`Transferring tokens from a source chain to a destination chain`](#Transferring-tokens-from-a-source-chain-to-a-destination-chain)
- [`Handling a cross-chain request`](#Handling-a-cross-chain-request)
- [`Handling the acknowledgement received from destination chain`](#Handling-the-acknowledgement-received-from-destination-chain)


# 🏗 Backend
  
## `Initiating the Contract`

For initiating the smart contract named "CrossChainERC20", the contract imports four external contracts :-

1. **ICrossTalkApplication.sol**

2. **IGateway.sol**

3. **ERC20.sol**

The "ICrossTalkApplication.sol" and "IGateway.sol" contracts are imported from the "evm-gateway-contract/contracts" and "ERC20.sol" from "openzeppelin/contracts/token".The "CrossChainERC20" contract implements the "ICrossTalkApplication" and "ERC20.sol" contract by inheriting from them. This means that the "CrossChainERC20" contract must have all the functions and variables defined in the "ICrossTalkApplication" contract. By importing and implementing these contracts, the "CrossChainERC20" contract will have access to their functionality and will be compatible with other contracts that follow the same standards.

```sh
//SPDX-License-Identifier: UNLICENSED
pragma solidity >=0.8.0 <0.9.0;

import "evm-gateway-contract/contracts/ICrossTalkApplication.sol";
import "evm-gateway-contract/contracts/IGateway.sol";
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract CrossChainERC20 is ERC20, ICrossTalkApplication {
}
```
## `Creating State Variables and the Constructor`

The smart contract has the following state variables:

1. **onwer** - an address variable which stores the address from which the contract has been deployed.

2. **gatewayContract** - an address variable which holds the address of the gateway contract. Gateway contracts are contracts which are pre-deployed on supported blockchains for cross-chain communication.The source chain's gateway contract communicates with the destination chain's gateway contract, enabling communication between application contracts deployed on different chains. Find GatewayContract Addresses [here](https://devnet.lcd.routerprotocol.com/router-protocol/router-chain/multichain/chain_config)

3. **destGasLimit** - a uint64 variable which indicates the amount of gas required to execute the function that will handle cross-chain requests on the destination chain.

4. **ourContractOnChains** - a mapping which maps a chain type and chain ID to the address of CrossChainERC20 contract deployed on different chains. This mapping will be used to set the address of the destination contract to the source contract and visa versa

The constructor of the smart contract takes three parameters:

1. **gatewayAddress** - an address variable which holds the address of the gateway contract.

2. **_destGasLimit** - a uint64 variable which indicates the amount of gas required to execute the function that will handle cross-chain requests on the destination chain.

Inside ERC20 contract contructor, pass the name of yout token followed by its symbol.

The smart contract extends the ERC20 standard and includes all the required functions and variables such as balanceOf, totalSupply, mint, burn and others.

```sh
 address owner;
 address public gatewayContract;
 uint64 public destGasLimit;
 mapping(uint64 => mapping(string => bytes)) public ourContractOnChains;
 constructor( address payable gatewayAddress,
        uint64 _destGasLimit ) ERC20("Token","RTK")
        {
            gatewayContract=gatewayAddress;
            destGasLimit=_destGasLimit;
            owner=msg.sender;
        }
```

## `Setting up the Destination Contract on the Source Contract`

**setContractOnChain**:-

The given code defines a setter function setContractOnChain which allows the owner to set the address of a destination contract on the source chain and visa versa. The function takes three parameters:

chainType - a uint64 variable which represents the type of the chain (e.g. Ethereum, Binance Smart Chain, etc.)
chainId - a string which represents the ID of the chain where the contract is deployed.
contractAddress - an address variable which holds the address of the CrossChainERC20 contract deployed on the other chain we want to send the tokens to.
The function first checks whether the caller is the owner by comparing the msg.sender with the admin address variable. If the caller is not the owner, the function will revert with an error message "only owner".

If the caller is the owner, the function will convert the contractAddress to bytes using the toBytes function and store it in the ourContractOnChains mapping using the chainType and chainId as the keys.

```sh
function setContractOnChain(
    uint64 chainType, 
    string memory chainId, 
    address contractAddress
) external {
    require(msg.sender == onwer, "only owner");
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
        uint64 _dstChainType,
        string memory _dstChainId, // it can be uint, why it is string?
        uint64 destGasPrice,
        address recipient,
        uint256 amount
    ) public {
        bytes memory payload = abi.encode(amount, recipient);

        // burn token on src chain from msg.msg.sender
        _burn(msg.sender, amount);

      

        bytes[] memory addresses = new bytes[](1);
        addresses[0] = ourContractOnChains[_dstChainType][_dstChainId];
        bytes[] memory payloads = new bytes[](1);
        payloads[0] = payload;

        IGateway(gatewayContract).requestToDest(
            Utils.RequestArgs(1000000000000000, false, Utils.FeePayer.APP),
            Utils.AckType(Utils.AckType.NO_ACK),
            Utils.AckGasParams(destGasLimit, destGasPrice),
            Utils.DestinationChainParams(
                destGasLimit,
                destGasPrice,
                _dstChainType,
                _dstChainId
            ),
            Utils.ContractCalls(payloads, addresses)
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
