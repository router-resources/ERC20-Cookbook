## Router Protocol

<!-- <p align="center" >

<img src="https://user-images.githubusercontent.com/124175970/224509096-12e4864a-6819-4c8c-8998-41c7a96ba026.jpg" />
  </p> -->

<!-- ![router-protocol-crypto-ninjas](https://user-images.githubusercontent.com/124175970/224509096-12e4864a-6819-4c8c-8998-41c7a96ba026.jpg) -->

<img src="https://user-images.githubusercontent.com/124175970/224509096-12e4864a-6819-4c8c-8998-41c7a96ba026.jpg" width="8000000em" height="400em" />


# `Company Information`

Router Protocol is a solution introduced to address the issues hindering the usability of cross-chain liquidity migration in the DeFi ecosystem. It acts as a bridge connecting various layer 1 and layer 2 blockchains, allowing for the flow of contract-level data across them. The Router Protocol can either transfer tokens between chains or initiate operations on one chain and execute them on another.

Please check the [official documentation of Router Protocol](https://www.routerprotocol.com/)
# 🧭 `Table of contents`
- [🧭 Table of contents](#-table-of-contents)
- [`What is an ERC20 Token ?`](#What-is-an-NFT-?)
- [`How to make a simple ERC20 Token ?`](#How-to-make-a-simple-NFT-?)
- [`Need for CrossChain ?`](#Need-for-CrossChain)
- [`What is a CrossChain ERC20 Token ?`](#What-is-CrossChain-NFT-?)
- [`Understanding Router CrossTalk`](#Understanding-Router-CrossTalk)
- [`CrossTalk Cheatsheet`](#Understanding-Router-CrossTalk)
- [`Initiating the Contract`](#Initiating-the-Contract)
- [`Creating state variables and the constructor`](#Creating-state-variables-and-the-constructor)
- [`Setting up the Destination Contract on the Source Contract`](#Setting-up-the-Destination-Contract-on-the-Source-Contract)
- [`Transferring tokens from a source chain to a destination chain`](#Transferring-tokens-from-a-source-chain-to-a-destination-chain)
- [`Handling a cross-chain request`](#Handling-a-cross-chain-request)
- [`Handling the acknowledgement received from destination chain`](#Handling-the-acknowledgement-received-from-destination-chain)
- [`Full Code`](#Full-Code)
- [🚀 Steps](#-quick-start)

## `What is an ERC20 Token ?`

![_117548721_nfts2](https://ph-files.imgix.net/a384cba0-ea83-4b99-b92b-06ab3639b493.gif?auto=format)

ERC20 Tokens are digital assets that are created using the Ethereum blockchain technology. They are programmable tokens that can be used to represent various types of assets, such as loyalty points, shares of stock, or even cryptocurrencies. ERC20 Tokens are fungible, which means that each token has the same value and can be exchanged for another token of the same value.

The "ERC20" part of the name refers to the technical standard that is used to create and manage these tokens. This standard defines the rules for creating new tokens and the functions that can be used to transfer and manage them.

## `How to make a simple ERC20 Token ?`

![ERC20](https://moralis.io/wp-content/uploads/2021/08/21_08_How-to-Create-Your-Own-ERC-20-Token-in-10-Minutes.jpg)

ERC20 is a standard for creating fungible tokens on the Ethereum blockchain. The ERC20 standard is like a set of rules that all tokens on the Ethereum blockchain must follow. Think of it like a recipe for making a soup - you need certain ingredients and instructions to make sure it turns out right.

ERC20 defines a specific set of functions that tokens must have, like the ability to be transferred from one address to another, the ability to check the token balance of an address, and the ability to approve another address to transfer tokens on behalf of the owner. These functions are like different steps in the recipe.

Some of the functions included in the ERC20 standard are:

**totalSupply:** This function returns the total number of tokens in existence.

**balanceOf:** This function returns the token balance of a specific address.

**transfer:** This function allows the owner of a token to transfer tokens to another address.

**approve:** This function allows an address to approve another address to transfer tokens on its behalf.

**allowance:** This function returns the amount of tokens that an approved address is allowed to transfer.

**transferFrom:** This function allows an approved address to transfer tokens on behalf of the owner.

Instead of writing all the code for creating an ERC20 token from scratch, developers can use OpenZeppelin's pre-written code to make their own tokens. This can save a lot of time and effort and can also help ensure that the code works correctly and is secure.

With OpenZeppelin, developers can just follow the pre-written code to create their own ERC20 tokens without having to start from scratch. It's kind of like using pre-made ingredients to cook a meal - instead of making each ingredient from scratch, you can just use pre-made ingredients to cook something faster and easier.

**Simple ERC20 contract using Openzeppelin**

```sh
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Burnable.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract MyToken is ERC20, ERC20Burnable, Ownable {
    constructor() ERC20("MyToken", "MTK") {}

    function mint(address to, uint256 amount) public onlyOwner {
        _mint(to, amount);
    }
}
```



You can use the above code and deploy using [Remix IDE](https://www.remix.ethereum.org) and Hurray !, you've made your own crypto currency.

## `Need for CrossChain`

**Bitcoin**

![images](https://user-images.githubusercontent.com/124175970/224867383-360da83c-5053-4f5b-aff3-8c9d1b00942e.png)

Back in 2009, Bitcoin was created by an unknown person called Satoshi Nakamoto, introducing the concept of decentralization. It eliminated the need for a central authority or intermediary to verify transactions, making it possible for people to transact with each other directly.

**Problem with Bitcoin**

Bitcoin was not designed to support smart contracts, which limits its ability to create complex decentralized applications. 
The inability to deploy smart contracts on the Bitcoin blockchain, makes the scope of decentralization , limited to a very small area.

**Ethereum**

![ethereum-1](https://user-images.githubusercontent.com/124175970/224869429-53317c46-6407-4fa7-b52a-76e691f333e9.jpeg)

Ethereum is a blockchain, which serves as a state machine where people can deploy smart contracts, This enables people to build Dapps (Decentralised Applications on the top of Ethereum" 

**Problem with Ethereum**

Ethereum is not scalable.The cost of gas fees which one need to pay to interact with the smart contracts is quite high. This hinders the businesses of the Dapps buit on the top of Ethereum.

**L2 Solutions of Ethereum**

<img width="400" alt="image" src="https://user-images.githubusercontent.com/124175970/224869825-bfcb3ac6-e0df-40fe-96b0-ca01ccacfe0b.png">

Many L2 solutions for Ethereum are created, keeping in mind Ethereum isn't scalable. The gas fee which one need to pay in order to intereact with the smart conracts is significantly cheaper than Ethereum.

**Problem with L2 solutions of Ethereum**

L2 solutions solves the problem of scalability , but they have much lesser nodes as compared to Ethereum making them less decentralised and secure than Ethereum.

**Blockchain Trilemma**

<img width="488" alt="image" src="https://user-images.githubusercontent.com/124175970/224870341-b8750142-f1e6-43cd-abf5-607fcc6add3e.png">

So, we can clearly see, solving scalability , degrades security and decentralisation and having more decentralisation and security results in making the chain less scalable .

This is known as Blockchain Trilemma, where it's not possible to ensure scalability, decentralisation and security at the same time . 

**Need for Crosschain**

Going CrossChain helps us leverage all these 3 features (Decentralisation + Scalabity + Security ) used for different operations performed in a single Dapp.
This is exactly , where the Router comes in ! It is an interoperability layer to connect blockchains with a goal to integrate all the blockchains within the ecosystem. 

<img width="581" alt="image" src="https://user-images.githubusercontent.com/124175970/224871477-a1f06b33-0b74-4244-9b3f-3291e246950d.png">

## `What is a CrossChain ERC20 Token ?`

CrossChain ERC20 tokens are tokens that can exist and be traded on multiple different blockchain networks. This means that an ERC20 token created on one blockchain network, such as Ethereum, can be moved to and traded on another blockchain network, such as Binance Smart Chain or Polygon.

Imagine you have an ERC20 token on the Ethereum blockchain that represents a digital asset. If you want to sell or trade that token on another blockchain network, you would need to create a new token on that network, which can be time-consuming and costly. However, with CrossChain ERC20 tokens, you can simply transfer the original token to the new blockchain network, enabling you to sell or trade it without having to go through the process of creating a new one.

<img width="461" alt="image" src="https://stealthex.io/blog/wp-content/uploads/2022/06/%D0%A1ross_chain_bridge-5-min.png">


## `Understanding Router CrossTalk`

**Overview**

Router's CrossTalk library is a tool that makes it easy for different blockchains to communicate with each other. This library is designed to work with Router's infrastructure, and allows contracts on one blockchain to talk to contracts on another blockchain. You can use this library in your own development projects to make it easier for your contracts to communicate across different blockchains, without disrupting other parts of your project.

**Gateway Contracts**

Gateway contracts are contracts which are pre-deployed on supported blockchains for cross-chain communication.The source chain's gateway contract communicates with the destination chain's gateway contract, enabling communication between application contracts deployed on different chains

[Gateway Contract Addresses](https://devnet-alpha.lcd.routerprotocol.com/router-protocol/router-chain/multichain/chain_config)

**CrossTalk Workflow**

<img width="503" alt="image" src="https://user-images.githubusercontent.com/124175970/224872866-cb82abb4-d072-4c7d-bc46-672a18afbb54.png">

When a user wants to execute a cross-chain request, they call the "iSend" function on the Router's Gateway contract. They pass the payload of data to be transferred from the source chain to the destination chain along with the necessary parameters. The "iSend" function sends the data to the destination chain where the user's contract with the "iRecieve" function is waiting to receive it.Once the data is received, the "iRecieve" function processes it on the destination chain. After processing the data, the destination chain sends an acknowledgment back to the source chain where "iAck" function in the user's contract is used to handle it.

**Understanding CrossTalk Functions**

Router’s Gateway contracts have a function named iSend that facilitates the transmission of a cross-chain message. Whenever users want to execute a cross-chain request, they can call this function by passing the payload to be transferred from the source to the destination chain along with the required parameters.

In addition to calling the aforementioned function, CrossTalk users will also have to implement two functions on their contracts:

To handle a cross-chain request on the destination chain, users are required to include a iRecieve function on their destination chain contracts.
To process the acknowledgment of their requests on the source chain, user will have to implement a iAck function on their source chain contracts.

**iSend parameters**

1. **version**: Current version of Gateway contract which can be queried from the Gateway contract using the following function.
2. **routeAmount**: If one wants to transfer Route tokens along with the call, they will have to pass the amount of tokens to be transferred here.
3. **routeRecipient:** If one wants to transfer Route tokens along with the call, they will have to pass the address of recipient on the destination chain to which Route tokens will be minted on destination chain. 
4. **destChainId:** Chain ID of the destination chain in string format.
5. **requestMetadata:** Some static information for the request. This is created so that iDapps don’t have to encode it on-chain, they can just send it as a parameter to their iDapp depending on the destination chain Id passed by the user. 
6. **requestPacket:** This is bytes encoded string consisting of two parameters:

    a. **destContractAddress:** This is the address of the smart contract on the destination chain which will handle the payload                that you send from the source chain to the destination chain.
    b. **payload:** This is bytes containing the payload that you want to send to the destination chain. This can be anything depending          on your utility. In this case ,it is the recipient address on destination chain and the amount of tokens to be sent on the                destination chain 


**iRecieve**

This function is called by the Gateway contract on the destination chain, which is triggered when a cross-chain transfer request is sent to the destination chain. This function receives 3 parameters:-

1. **requestSender**: A bytes array that represents the address of the contract on the source chain that initiated the cross-chain transfer request.

2. **packet**: A bytes array that containing the payload we sent from the source chain.

3. **srcChainId**: A string that represents the ID of the source chain from which the cross-chain transfer request originated.




![Click here to Open Router CrossTalk Cheatsheet](https://user-images.githubusercontent.com/124175970/224877602-3e7c7bd2-700c-4ac8-96f5-0e391f8859bf.png)

[Click here to download Router CrossTalk Cheatsheet](https://github.com/router-resources/CrossChainNFTAssignment/files/10963540/crosstalk.pdf)



# `CrossChain ERC-20`

> Effortlessly transfer ERC-20 tokens from one chain to another. Made using Router Cross-Talk.

This project is built with [Router CrossTalk](https://dev.routerprotocol.com/crosstalk-library/overview/introduction)

Router Protocol is a solution introduced to address the issues hindering the usability of cross-chain liquidity migration in the DeFi ecosystem. It acts as a bridge connecting various layer 1 and layer 2 blockchains, allowing for the flow of contract-level data across them. The Router Protocol can either transfer tokens between chains or initiate operations on one chain and execute them on another.

Please check the [official documentation of Router Protocol](https://www.routerprotocol.com/) 

![1234](https://user-images.githubusercontent.com/124175970/222918389-d6d0a046-bf4c-4737-8577-35794efba8f2.gif)


# ⭐️ `Star us`

If this repository helps you build cross-chain dapps faster and easier - please star this project, every star makes us very happy!

# 🤝 `Need help?`

If you need help or have other some questions - don't hesitate to write in our discord channel and we will check asap. [Discord link](https://discord.gg/xvx2pFu9). The best thing about this is the super active community ready to help at any time! We help each other.

# 🤝 `Clone or fork this repository`

```sh
git clone https://github.com/router-resources/ERC20-Cookbook.git
```

# 🎯 `Steps`


✍️ **Setting up your editor:**

Browse to [Remix IDE](https://remix.ethereum.org/) and create a new file with ".sol" extension.

💿 **Install all dependencies:**

You don't need to install any dependencies. Remix automatically downloads all the dependencies for you during the time of compile.

🧑‍💻 **Create your CrossChain ERC-20 token Contract:**

To create the contract for your CrossChain ERC-20 token , copy-paste the [`Code`](#Full-Code) in the Remix Work Area and compile it.
The Code has been comprehensively explained in this repository. Click [`here`](#Initiating-the-Contract) for the explanation.

🚀 **Deploying the Contract:**

You need to deploy the same contract on the source chain as well as the destination chain and pass in the required parameters to the [`constructer`](#Creating-state-variables-and-the-constructor) while deploying.

🔨 **Mint created ERC-20 token on Source Chain:**

In order to mint the created ERC-20 token on the source , mint function defined in openzeppelin can be used.

🤝 **Set destination contract to source contract and source contract to destination contract:**

To set destination contract to source contract and source contract to destination contract, we make use of setContractOnChain function. For more info, go to [`Setting up the Destination Contract on the Source Contract`](#Setting-up-the-Destination-Contract-on-the-Source-Contract)

💵 **Get Route Test Tokens on your wallet address :**

To get Route tokens on wallet address, copy the source contract address, visit https://devnet-faucet.routerprotocol.com/ , paste the address there and click on Get Route

🚂 **Transfer minted ERC-20 tokens from source chain to destination chain:**

To transfer minted ERC-20 tokens from source chain to destination chain, we make use of transferCrosschain function, which burns specified amount of tokens on source chain and mint the same amount on the destination chain. For more info, go to [`Transferring tokens from a source chain to a destination chain`](#Transferring-tokens-from-a-source-chain-to-a-destination-chain)

🔍 **Browse to [Router Testnet Explorer](https://explorer.testnet.routerchain.dev/crosstalks)** to see the transactions made. Wait for sometime till you see 4 green checks in your transaction column.This indicates, the tokens have been successfully transferred to the destination chain

📖 For more detailed steps , refer [Step by Step guide for CrossChain ERC-20](https://github.com/router-resources/Workshop-ERC20)



  
## `Initiating the Contract`

For initiating the smart contract named "CrossChainERC20", the contract imports four external contracts :-

1. **IDapp.sol**

2. **IGateway.sol**

3. **Utils.sol**

4. **ERC20.sol**

The "IDapp.sol" and "IGateway.sol" contracts are imported from the "evm-gateway-contract/contracts" and "ERC20.sol" from "openzeppelin/contracts/token".The "CrossChainERC20" contract implements the "IDapp" and "ERC20.sol" contract by inheriting from them. This means that the "CrossChainERC20" contract must have the functions and variables defined in the "IDapp" contract. By importing and implementing these contracts, the "CrossChainERC20" contract will have access to their functionality .

```sh
//SPDX-License-Identifier: UNLICENSED
pragma solidity >=0.8.0 <0.9.0;

import "@routerprotocol/evm-gateway-contracts/contracts/IDapp.sol";
import "@routerprotocol/evm-gateway-contracts/contracts/IGateway.sol";
import "@routerprotocol/evm-gateway-contracts/contracts/Utils.sol";
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

contract CrossChainERC20 is ERC20, IDapp {
}
```
## `Creating State Variables and the Constructor`

The smart contract has the following state variables:

1. **onwer** - an address variable which stores the address from which the contract has been deployed.

2. **gatewayContract** - an address variable which holds the address of the gateway contract. Gateway contracts are contracts which are pre-deployed on supported blockchains for cross-chain communication.The source chain's gateway contract communicates with the destination chain's gateway contract, enabling communication between application contracts deployed on different chains. Find GatewayContract Addresses [here](https://lcd.testnet.routerchain.dev//router-protocol/router-chain/multichain/chain_config)

3. **destGasLimit** - a uint64 variable which indicates the amount of gas required to execute the function that will handle cross-chain requests on the destination chain.

4. **ourContractOnChains** - a mapping which maps a chain type and chain ID to the address of CrossChainERC20 contract deployed on different chains. This mapping will be used to set the address of the destination contract to the source contract and visa versa

The constructor of the smart contract takes three parameters:

1. **gatewayAddress** - an address variable which holds the address of the gateway contract.

2. **feePayerAddress** - a string variable which holds the address on the Router Chain from which the fees is deducted.

Inside ERC20 contract contructor, pass the name of your token followed by its symbol.

The smart contract extends the ERC20 standard and includes all the required functions and variables such as balanceOf, totalSupply, mint, burn and others.

```sh
address public owner;

  IGateway public gatewayContract;

  uint64 public _destGasLimit;

  mapping(string => bytes) public ourContractOnChains;

  constructor(
    address payable gatewayAddress,
    string memory feePayerAddress
  ) ERC20("My Token", "MTK") {
    gatewayContract = IGateway(gatewayAddress);
    owner = msg.sender;
    gatewayContract.setDappMetadata(feePayerAddress);
  }

```

## `Setting up the Destination Contract on the Source Contract`

**setContractOnChain**:-

The given code defines a setter function setContractOnChain which allows the owner to set the address of a destination contract on the source chain and visa versa. The function takes 2 parameters:

chainId - a string which represents the ID of the chain where the contract is deployed.
contractAddress - an address variable which holds the address of the CrossChainERC20 contract deployed on the other chain we want to send the tokens to.
The function first checks whether the caller is the owner by comparing the msg.sender with the admin address variable. If the caller is not the owner, the function will revert with an error message "only owner".

If the caller is the owner, the function will convert the contractAddress to bytes using the toBytes function and store it in the ourContractOnChains mapping using the chainType and chainId as the keys.

```sh
 function setContractOnChain(
    string memory chainId,
    address contractAddress
  ) external {
    require(msg.sender == owner, "only owner");
    ourContractOnChains[chainId] = toBytes(contractAddress);
  }
```
## `Transferring tokens from a source chain to a destination chain`

**transferCrossChain:-**

This function allows a user to transfer their tokens from their account on source chain to their account on a destination chain. The function burns the specified amount of tokens from the user's account, creates a cross-chain communication request to the destination chain, and passes the transfer parameters as payload in the request.

The function accepts the following parameters:

1. **amount**: A uint256 variable specifying the amount of tokens we want to transfer to the recipient on the destination chain

2. **_dstchainId**: A string representing the ID of the destination chain.

3. **recipient**: Wallet address on the destination chain we want to transfer our tokens to.

5. **requestMetadata**: Some static information for the request. This is created so that iDapps don’t have to encode it on-chain, they can just send it as a parameter to their iDapp depending on the destination chain Id passed by the user. The request metadata is a bytes encoded string consisting of the following parameters: 
```sh
    uint64 destGasLimit;
    uint64 destGasPrice;
    uint64 ackGasLimit;
    uint64 ackGasPrice;
    uint128 relayerFees;
    uint8 ackType;
    bool isReadCall;
    string asmAddress;
```
The function burns the amount of tokens specified in the variable "amount" from the user's account and creates a cross-chain communication request to the destination chain. 

The function calls the iSend Function of the Gateway Contract to generate the cross-chain communication request to the destination chain.The iSend Function function is used to send a request to the Destination Chain .To execute a cross-chain request, users can call this function and pass the payload and required parameters from the source to the destination chain.

**iSend Function** function takes in these parameters:-

1. **version**: Current version of Gateway contract which can be queried from the Gateway contract using the following function.
2. **routeAmount**: If one wants to transfer Route tokens along with the call, they will have to pass the amount of tokens to be transferred here.
3. **routeRecipient:** If one wants to transfer Route tokens along with the call, they will have to pass the address of recipient on the destination chain to which Route tokens will be minted on destination chain. 
4. **destChainId:** Chain ID of the destination chain in string format.
5. **requestMetadata:** Some static information for the request. This is created so that iDapps don’t have to encode it on-chain, they can just send it as a parameter to their iDapp depending on the destination chain Id passed by the user. 
6. **requestPacket:** This is bytes encoded string consisting of two parameters:

    a. **destContractAddress:** This is the address of the smart contract on the destination chain which will handle the payload                that you send from the source chain to the destination chain.
    b. **payload:** This is bytes containing the payload that you want to send to the destination chain. This can be anything depending          on your utility. In this case ,it is the recipient address on destination chain and the amount of tokens to be sent on the                destination chain 
    
```sh
function transferCrossChain(
    uint256 amount,
    string calldata destChainId,
    string calldata recipient,
    bytes calldata requestMetadata
  ) public payable {
    require(
      keccak256(ourContractOnChains[destChainId]) !=
        keccak256(toBytes(address(0))),
      "contract on dest not set"
    );

    require(
      balanceOf(msg.sender) >= amount,
      "ERC20: Amount cannot be greater than the balance"
    );

   
    _burn(msg.sender, amount);

    // encoding the data that we need to use on destination chain to mint the tokens there.
    bytes memory packet = abi.encode(recipient, amount);
    bytes memory requestPacket = abi.encode(
      ourContractOnChains[destChainId],
      packet
    );

    gatewayContract.iSend{ value: msg.value }(
      1,
      0,
      string(""),
      destChainId,
      requestMetadata,
      requestPacket
    );
  }
  ```
 
## `Handling a cross-chain request`

**iReceive:-**

This function is called by the Gateway contract on the destination chain, which is triggered when a cross-chain transfer request is sent to the destination chain. This function receives 3 parameters:-

1. **requestSender**: A bytes array that represents the address of the contract on the source chain that initiated the cross-chain transfer request.

2. **packet**: A bytes array that containing the payload we sent from the source chain.

3. **srcChainId**: A string that represents the ID of the source chain from which the cross-chain transfer request originated.

The function first checks that the call is made only by the Gateway contract and that the request is received from our contract on the source chain. If the conditions are not met, the function will revert the transaction.

The packet that was sent with the cross-chain transfer request contains recipient's address, the amount of tokens to be minted on the destination chain. The function decodes the packet using abi.decode() function.

After decoding the packet, the function uses the _mint function of the ERC-20 contract from the OpenZeppelin library to mint the ERC-20 tokens to the recipient's address on the destination chain. 

Finally, the function returns an empty string. Note : We have to return atleast an empty string as per the function definition.

```sh
function iReceive(
    string memory requestSender,
    bytes memory packet,
    string memory srcChainId
  ) external override returns (bytes memory) {
    require(msg.sender == address(gatewayContract), "only gateway");
    require(
      keccak256(ourContractOnChains[srcChainId]) ==
        keccak256(bytes(requestSender))
    );

    (bytes memory recipient, uint256 amount) = abi.decode(
      packet,
      (bytes, uint256)
    );
    _mint(toAddress(recipient), amount);

    return abi.encode(srcChainId);
  }
```
## `Getting metadata for the request`

Some static information for the request. This is created so that iDapps don’t have to encode it on-chain, they can just send it as a parameter to their iDapp depending on the destination chain Id passed by the user. The request metadata is a bytes encoded string consisting of the following parameters: 

1. **destGasLimit:** Gas limit required for execution of the request on the destination chain. This can be calculated using tools like [hardhat-gas-reporter](https://www.npmjs.com/package/hardhat-gas-reporter).

2. **destGasPrice:** Gas price of the destination chain. This can be calculated using the RPC of destination chain.
If you don’t want to calculate it, just send `0` in its place and Router Chain will handle the real time gas price for you. 

3. **ackGasLimit:** Gas limit required for execution of the acknowledgement coming from the destination chain back on the source chain. This can be calculated using tools like [hardhat-gas-reporter](https://www.npmjs.com/package/hardhat-gas-reporter).

4. **ackGasPrice:** Gas price of the destination chain. This can be calculated using the RPC of source chain as shown in the above [snippet](https://www.notion.so/EVM-to-Other-Chain-Flow-de922b13e0fa4d7b8c3c24590ff8ef65).

5. **relayerFees:** This is similar to priority fees that one pays on other chains. Router chain relayers execute your requests on the destination chain. So if you want your request to be picked up by relayer faster, this should be set to a higher number. If you pass really low amount, the Router chain will adjust it to some minimum amount.

6. **ackType:** When the contract calls have been executed on the destination chain, the iDapp has the option to get an acknowledgement back to the source chain.
    
We provide the option to the user to be able to get this acknowledgement from the router chain to the source chain and perform some operation based on it.
    
  1. **ackType = 0:** You don’t want the acknowledgement to be forwarded back to the source chain.
  2. **ackType = 1:** You only want to receive the acknowledgement back to the source chain in case the calls executed successfully on the destination chain and perform some operation after that.
  3. **ackType = 2:** You only want to receive the acknowledgement back to the source chain in case the calls errored on the destination chain and perform some operation after that.
  4. **ackType = 3:** You only want to receive the acknowledgement back to the source chain in both the cases (success and error) and perform some operation after that.
  
7. **isReadCall:** We provide you the option to query a contract from another chain and get the data back on the source chain through acknowledgement. If you just want to query a contract on destination chain, set this to `true`.

8. **asmAddress:** We also provide modular security framework for creating an additional layer of security on top of the security provided by Router Chain. These will be in the form of smart contracts on destination chain. The address of this contract needs to be passed in the form of string in this variable.
    
    The request metadata can be constructed using the following code:
    
```sh
function getRequestMetadata(
    uint64 destGasLimit,
    uint64 destGasPrice,
    uint64 ackGasLimit,
    uint64 ackGasPrice,
    uint128 relayerFees,
    uint8 ackType,
    bool isReadCall,
    string calldata asmAddress
  ) public pure returns (bytes memory) {
    bytes memory requestMetadata = abi.encodePacked(
      destGasLimit,
      destGasPrice,
      ackGasLimit,
      ackGasPrice,
      relayerFees,
      ackType,
      isReadCall,
      asmAddress
    );
    return requestMetadata;
  }
```
## `Handling the acknowledgement received from destination chain`

**iAck:-**

The iAck function is a public function that needs to be implemented in the contract to satisfy the IDapp interface.
The function takes three parameters: **eventIdentifier**, **execFlags**, and **execData** . However, since we are only implementing an empty function, we do not need to use these parameters.

The iAck function does not perform any operation here. It is implemented as an empty function and only serves as a placeholder to satisfy the interface requirements.

Therefore, the iAck function should be implemented with an empty body as shown in the code provided.

```sh
function iAck(
    uint256 requestIdentifier,
    bool execFlag,
    bytes memory execData
  ) external override {}

```

## `Full Code`

```sh
// SPDX-License-Identifier: UNLICENSED
pragma solidity >=0.8.0 <0.9.0;

import "@routerprotocol/evm-gateway-contracts/contracts/IDapp.sol";
import "@routerprotocol/evm-gateway-contracts/contracts/IGateway.sol";
import "@routerprotocol/evm-gateway-contracts/contracts/Utils.sol";
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

/// @title XERC20
/// @author Yashika Goyal
/// @notice This is a cross-chain ERC-20 smart contract to demonstrate how one can
/// utilise Router CrossTalk for making cross-chain tokens
contract XERC20 is ERC20, IDapp {
  // address of the owner
  address public owner;

  // address of the gateway contract
  IGateway public gatewayContract;

  // gas limit required to handle cross-chain request on the destination chain
  uint64 public _destGasLimit;

  // chain id => address of our contract in bytes
  mapping(string => bytes) public ourContractOnChains;

  constructor(
    address payable gatewayAddress,
    string memory feePayerAddress
  ) ERC20("My Token", "MTK") {
    gatewayContract = IGateway(gatewayAddress);
    owner = msg.sender;

    //minting 20 tokens to deployer initially for testing
    _mint(msg.sender, 20);

    gatewayContract.setDappMetadata(feePayerAddress);
  }

  /// @notice function to set the fee payer address on Router Chain.
  /// @param feePayerAddress address of the fee payer on Router Chain.
  function setDappMetadata(string memory feePayerAddress) external {
    require(msg.sender == owner, "only owner");
    gatewayContract.setDappMetadata(feePayerAddress);
  }

  /// @notice function to set the Router Gateway Contract.
  /// @param gateway address of the gateway contract.
  function setGateway(address gateway) external {
    require(msg.sender == owner, "only owner");
    gatewayContract = IGateway(gateway);
  }

  function mint(address account, uint256 amount) external {
    require(msg.sender == owner, "only owner");
    _mint(account, amount);
  }

  /// @notice function to set the address of our ERC20 contracts on different chains.
  /// This will help in access control when a cross-chain request is received.
  /// @param chainId chain Id of the destination chain in string.
  /// @param contractAddress address of the ERC20 contract on the destination chain.
  function setContractOnChain(
    string memory chainId,
    address contractAddress
  ) external {
    require(msg.sender == owner, "only owner");
    ourContractOnChains[chainId] = toBytes(contractAddress);
  }

  /// @notice function to generate a cross-chain token transfer request.
  /// @param destChainId chain ID of the destination chain in string.
  /// @param recipient address of the recipient of tokens on destination chain
  /// @param amount amount of tokens to be transferred cross-chain
  /// @param requestMetadata abi-encoded metadata according to source and destination chains
  function transferCrossChain(
    uint256 amount,
    string calldata destChainId,
    string calldata recipient,
    bytes calldata requestMetadata
  ) public payable {
    require(
      keccak256(ourContractOnChains[destChainId]) !=
        keccak256(toBytes(address(0))),
      "contract on dest not set"
    );

    require(
      balanceOf(msg.sender) >= amount,
      "ERC20: Amount cannot be greater than the balance"
    );

    // burning the tokens from the address of the user calling this function
    _burn(msg.sender, amount);

    // encoding the data that we need to use on destination chain to mint the tokens there.
    bytes memory packet = abi.encode(recipient, amount);
    bytes memory requestPacket = abi.encode(
      ourContractOnChains[destChainId],
      packet
    );

    gatewayContract.iSend{ value: msg.value }(
      1,
      0,
      string(""),
      destChainId,
      requestMetadata,
      requestPacket
    );
  }

  /// @notice function to get the request metadata to be used while initiating cross-chain request
  /// @return requestMetadata abi-encoded metadata according to source and destination chains
  function getRequestMetadata(
    uint64 destGasLimit,
    uint64 destGasPrice,
    uint64 ackGasLimit,
    uint64 ackGasPrice,
    uint128 relayerFees,
    uint8 ackType,
    bool isReadCall,
    string calldata asmAddress
  ) public pure returns (bytes memory) {
    bytes memory requestMetadata = abi.encodePacked(
      destGasLimit,
      destGasPrice,
      ackGasLimit,
      ackGasPrice,
      relayerFees,
      ackType,
      isReadCall,
      asmAddress
    );
    return requestMetadata;
  }

  /// @notice function to handle the cross-chain request received from some other chain.
  /// @param requestSender address of the contract on source chain that initiated the request.
  /// @param packet the payload sent by the source chain contract when the request was created.
  /// @param srcChainId chain ID of the source chain in string.
  function iReceive(
    string memory requestSender,
    bytes memory packet,
    string memory srcChainId
  ) external override returns (bytes memory) {
    require(msg.sender == address(gatewayContract), "only gateway");
    require(
      keccak256(ourContractOnChains[srcChainId]) ==
        keccak256(bytes(requestSender))
    );

    (bytes memory recipient, uint256 amount) = abi.decode(
      packet,
      (bytes, uint256)
    );
    _mint(toAddress(recipient), amount);

    return abi.encode(srcChainId);
  }

  /// @notice function to handle the acknowledgement received from the destination chain
  /// back on the source chain.
  /// @param requestIdentifier event nonce which is received when we create a cross-chain request
  /// We can use it to keep a mapping of which nonces have been executed and which did not.
  /// @param execFlag a boolean value suggesting whether the call was successfully
  /// executed on the destination chain.
  /// @param execData returning the data returned from the handleRequestFromSource
  /// function of the destination chain.
  function iAck(
    uint256 requestIdentifier,
    bool execFlag,
    bytes memory execData
  ) external override {}

  /// @notice function to convert type address into type bytes.
  /// @param a address to be converted
  /// @return b bytes pertaining to the address
  function toBytes(address a) public pure returns (bytes memory b) {
    assembly {
      let m := mload(0x40)
      a := and(a, 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF)
      mstore(add(m, 20), xor(0x140000000000000000000000000000000000000000, a))
      mstore(0x40, add(m, 52))
      b := m
    }
  }

  /// @notice Function to convert bytes to address
  /// @param _bytes bytes to be converted
  /// @return addr address pertaining to the bytes
  function toAddress(bytes memory _bytes) internal pure returns (address addr) {
    bytes20 srcTokenAddress;
    assembly {
      srcTokenAddress := mload(add(_bytes, 0x20))
    }
    addr = address(srcTokenAddress);
  }
}
```
