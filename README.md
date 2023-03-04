# `Cross-Chat`

> Fully ready ReactJS Dapp for Cross-Chain Messaging using Router Cross-Talk

🚀DEMO: https://cross-chat-47b25.web.app/

This project is built with [Router CrossTalk](https://dev.routerprotocol.com/crosstalk-library/overview/introduction)

Router Protocol is a solution introduced to address the issues hindering the usability of cross-chain liquidity migration in the DeFi ecosystem. It acts as a bridge connecting various layer 1 and layer 2 blockchains, allowing for the flow of contract-level data across them. The Router Protocol can either transfer tokens between chains or initiate operations on one chain and execute them on another.

Please check the [official documentation of Router Protocol](https://www.routerprotocol.com/) 

![cross-chat](https://firebasestorage.googleapis.com/v0/b/cross-chat-47b25.appspot.com/o/image.gif?alt=media&token=51706ade-52ea-4a41-9a73-c5885a039c91)

# ⭐️ `Star us`

If this repository helps you build cross-chain dapps faster and easier - please star this project, every star makes us very happy!

# 🤝 `Need help?`

If you need help or have other some questions - don't hesitate to write in our discord channel and we will check asap. [Discord link](https://discord.gg/xvx2pFu9). The best thing about this is the super active community ready to help at any time! We help each other.

# 🚀 `Quick Start`

📄 Clone or fork `cross-chat`:

```sh
git clone https://github.com/protocol-designer/cross-chat.git
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
- [🏗 Frontend](#React JS, Moralis)
  - [`Provider`](#Provider)
  - [`Basic imports & setup`](#Basic-imports-&-setup)
  - [`Authentication`](#Authentication)
  - [`handleCreate`](#handleCreate)
  - [`addMessage`](#addMessage)
  - [`getContacts`](#getContacts)
  - [`getMessage`](#getMessage)
- [🏗 Backend](#Solidity, Router Cross-Talk Library)
  - [`Initiating the Contract`](#Initiating-the-Contract)
  - [`Creating state variables and the constructor`](#Creating-state-variables-and-the-constructor)
  - [`Creating a channel/ Sending a message to an address on destination chain`](#Creating-a-channel/Sending-a-message-to-an-address-on-destination-chain)
  - [`Handling a crosschain request`](#Handling-a-crosschain-request)
  - [`Deployments`](#Deployments)
  - [`Back to Frontend`](#Back-to-Frontend)
  
# 🏗 Frontend

# `Provider`

This project uses the Moralis library for interacting with the Ethereum blockchain. Moralis is a provider for the Web3.js library, which allows for communication with Ethereum nodes.

In the Index.js file, the React app has been wrapped with the MoralisProvider component provided by the Moralis library. This sets up the Moralis instance and makes it available to the rest of the app through the React context API.

For more information on Moralis, visit their official website at https://moralis.io/.

As an alternative, one could use the Ether.js or Web3.js libraries for similar functionality. The choice of Moralis in this project was made due to its ease of use and comprehensive documentation.

```sh
const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <MoralisProvider 
  >
    <App />
  </MoralisProvider>
);
```

# `Basic imports & setup`

In order to interact with the blockchain using Moralis, you need to import the useMoralis and useWeb3Contract hooks from the react-moralis library

```sh
import { useMoralis, useWeb3Contract } from 'react-moralis';
```
Next, you can use the useMoralis hook to get information about the the user's account and chain ID.

```sh
const {
  isWeb3Enabled,
  enableWeb3,
  account,
  chainId: chainIdHex,
} = useMoralis();
```
The useWeb3Contract hook is used to run functions on smart contracts deployed on the blockchain.
```sh
const { runContractFunction } = useWeb3Contract();
```

# `Authentication`

To ensure that the user has a Web3 enabled wallet connected, you can check the isWeb3Enabled value returned by the useMoralis hook. If it is false, you can prompt the user to connect their wallet by rendering a button that calls the enableWeb3 function.

![Authentication](https://firebasestorage.googleapis.com/v0/b/cross-chat-47b25.appspot.com/o/Authentication.png?alt=media&token=8cbcf306-f736-46e4-a3ac-96ddbb74274a)


```sh
if (!isWeb3Enabled) {
  return (
    <div>
      <h2>CrossChat</h2>
      <button
        onClick={async () => await enableWeb3()}
      >
        Connect Wallet
      </button>
    </div>
  );
}
```
When the user clicks the "Connect Wallet" button, the enableWeb3 function will be called, which will prompt the user to connect their Web3 enabled wallet (e.g. MetaMask).

Once the user has connected their wallet, the isWeb3Enabled value will change to true, allowing you to proceed with interacting with the Ethereum blockchain through Moralis.

# `handleCreate`

In order to create a channel between two wallets , a NULL message must be sent from one wallet to the other. This can be achieved by calling pingDestination function of the Cross-Talk Library using the runContractFunction function provided by the useWeb3Contract hook.

![handleCreate](https://firebasestorage.googleapis.com/v0/b/cross-chat-47b25.appspot.com/o/createHandle.png?alt=media&token=489d3998-23c1-4d47-b315-87c8365166e9)

The following code shows an example of how to use the runContractFunction function to create a channel between two wallets.

```sh
const handleCreate = async () => {
  const options = {
    abi: abi,
    contractAddress: contractAddress,
    functionName: "pingDestination",
    params: {
      chainId: "80001",
      destinationContractAddress: destContractAdd,
      user0: account,
      user1: channelReceiptAddress,
      message: "#NULL#",
    },
  };

  const result = await runContractFunction({ params: options });
  console.log(result, account);
};
```
The abi variable should contain the ABI (Application Binary Interface) of the source contract that is deployed on the Ethereum blockchain. The contractAddress variable should contain the address of the deployed source contract.

The pingDestination function is called with the following parameters:

chainId: The ID of the destination chain.
destinationContractAddress: The address of the destination contract.
user0: The address of the sender's wallet.
user1: The address of the receiver's wallet.
message: A NULL message to create the channel.
Once the runContractFunction function is called, a channel will be created between the two wallets.

# `addMessage`

To send a message through a previously created channel between two wallets, the addMessage function can be used. The function is similar to the handleCreate function that was used to create the channel, but with a text message passed to the pingDestination function instead of a NULL message.

![addMessage](https://firebasestorage.googleapis.com/v0/b/cross-chat-47b25.appspot.com/o/addMessage.png?alt=media&token=2a4f3eab-63b3-4276-81e9-e8876d3fcb8c)

```sh
const addMessage = async () => {
  const options = {
    abi: abi,
    contractAddress: contractAddress,
    functionName: "pingDestination",
    params: {
      chainId: "80001",
      destinationContractAddress: destContractAdd,
      user0: account,
      user1: receiptAddress,
      message: msg,
    },
  };
  const result = await runContractFunction({ params: options });
  console.log(result, account);
};
```
The abi and contractAddress variables are the same as in the handleCreate function, and they should contain the ABI and address of the source contract deployed.
The pingDestination function is called with the following parameters:

chainId: The ID of the destination chain.
destinationContractAddress: The address of the destination contract.
user0: The address of the sender's wallet.
user1: The address of the receiver's wallet.
message: The text message to be sent through the channel.
Once the runContractFunction function is called, the text message will be sent through the channel between the two wallets.

# `getContacts`

The getContacts function is used to retrieve the contacts with whom a channel has been created. It calls the getContacts function defined in the deployed smart contract.The getContacts takes in just one parameter ,u0 which is the sender's wallet address.

![getContacts](https://firebasestorage.googleapis.com/v0/b/cross-chat-47b25.appspot.com/o/getContacts1.png?alt=media&token=938a73e0-acd9-4d8b-8a37-1175559f9a6b)

```sh
  const getContact = async () => {
    const options3 = {
      abi: abi,
      contractAddress: destContractAdd,
      functionName: "getContacts",
      params: { u0: account },
    };
    let something = await runContractFunction({ params: options3 });
    
  }; 
```

# `getMessage`

The getMessages function retrieves the messages sent through a channel between two wallets. It calls the getMessages function defined in the deployed smart contract [which will be further discussed later in the readme]. It just takes in two parameter , u0 and u1 which are the wallet addresses of sender and receiver respectively.

![getMessage](https://firebasestorage.googleapis.com/v0/b/cross-chat-47b25.appspot.com/o/getMessage.png?alt=media&token=bcbac1fb-0dff-44dd-9605-6c0feeab0c76)

```sh
const getMessages = async () => {
    const options3 = {
      chainId: chainId,
      abi: abi,
      contractAddress: destContractAdd,
      functionName: "getMessages",
      params: { u0: account, u1: receiptAddress },
    };
    let something = await runContractFunction({ params: options3 });
    console.log(something);
    setMessage(something);
  };
  ```
  
# 🏗 Backend
  
# `Initiating the Contract`

For initiating the smart contract named "CrossChat", the contract imports three external contracts
The "ICrossTalkApplication.sol", "Utils.sol" and "IGateway.sol" contracts are imported from the "evm-gateway-contract/contracts" 
The "CrossChat" contract implements the "ICrossTalkApplication" contract by inheriting from it. This means that the "CrossChat" contract must have all the functions and variables defined in the "ICrossTalkApplication" contract.
By importing and implementing these contracts, the "CrossChat" contract will have access to their functionality and will be compatible with other contracts that follow the same standards.

```sh
//SPDX-License-Identifier: UNLICENSED
pragma solidity >=0.8.0 <0.9.0;

import "evm-gateway-contract/contracts/IGateway.sol";
import "evm-gateway-contract/contracts/ICrossTalkApplication.sol";
import "evm-gateway-contract/contracts/Utils.sol";
```
# `Initiating the Contract`

Creating State Variables and the Constructor
In this section of the smart contract code, state variables and events are defined for the contract in the Solidity programming language. The code defines the following:

A public state variable "gatewayContract" of type "address" is created to store the address of the gateway contract. This contract will be responsible for routing messages to the Router Chain.

A public state variable "greeting" of type "string" is created. This variable will store a message that will be set on the destination chain when a "message" is received.

A public state variable "lastEventIdentifier" of type "uint64" is created. This variable will store the nonce for the cross-chain transaction generated by the Gateway contract. This will be used to verify the nonce when the acknowledgment is received from the destination chain.

A public state variable "destGasLimit" of type "uint64" is created. This variable indicates the amount of gas required to execute the function that will handle the cross-chain request on the destination chain. This value can be easily calculated using a gas estimator such as the "hardhat-gas-reporter" plugin.

A public state variable "ackGasLimit" of type "uint64" is created. This variable indicates the amount of gas required to execute the callback function that will handle the acknowledgment received from the destination chain. This value can be easily calculated using a gas estimator such as the "hardhat-gas-reporter" plugin.

A custom error "CustomError" is created, which can be used to throw custom errors.

An event "ExecutionStatus" is created with parameters "uint64 eventIdentifier" and "bool isSuccess". This event will be emitted when the acknowledgment is received and handled by the source chain.

An event "ReceivedSrcChainIdAndType" is created with parameters "uint64 chainType" and "string chainID". This event will be emitted when the acknowledgment is received and handled by the source chain.

A constructor is created with the parameters "address payable gatewayAddress", "uint64 _destGasLimit", and "uint64 _ackGasLimit". The constructor sets the values of the "gatewayContract", "destGasLimit", and "ackGasLimit" state variables using the provided parameters.

```sh
address public gatewayContract;
string public greeting;
uint64 public lastEventIdentifier;
uint64 public destGasLimit;
uint64 public ackGasLimit;

error CustomError(string message);
event ExecutionStatus(uint64 eventIdentifier, bool isSuccess);
event ReceivedSrcChainIdAndType(uint64 chainType, string chainID);

constructor(
	address payable gatewayAddress, 
	uint64 _destGasLimit, 
	uint64 _ackGasLimit
) {
  gatewayContract = gatewayAddress;
	destGasLimit = _destGasLimit;
	ackGasLimit = _ackGasLimit;
}
```

# `Creating a channel/ Sending a message to an address on destination chain`

pingDestination Function:-
The pingDestination function is used to create a channel and send a message to a specified address on a destination chain. It is a public function that is payable.

Parameters
The function takes in the following parameters:

chainId (string memory): the id of the destination chain,
destinationContractAddress (address): the address of the destination contract to which the message will be sent,
user0 (address): the wallet address of the sender,
user1 (address): the wallet address of the receiver,
message (string memory): the message to be sent.
Functionality

The pingDestination function Increments the currentRequestId,
Encodes the currentRequestId, user0, user1, and message into a payload to be sent to the destination chain,
Sets the expiryTimestamp to be the current block timestamp plus 100000000000,
Converts the destinationContractAddress into an array of bytes called addresses,
Assigns the payload to an array of bytes called payloads,
Calls the _pingDestination function and passes the following parameters:
expiryTimestamp
80000000000 as source and destination chain gas limit
chainId
payloads
addresses

```sh
Creating a channel/ Sending a message to an address on destination chain

function pingDestination(string memory chainId, address destinationContractAddress, address user0, address user1, string memory message) public payable {
currentRequestId++;
bytes memory payload = abi.encode(currentRequestId, user0, user1, message);
uint64 expiryTimestamp = uint64(block.timestamp) + 100000000000;
bytes[] memory addresses = new bytes;
addresses[0] = toBytes(destinationContractAddress);
bytes[] memory payloads = new bytes;
payloads[0] = payload;
_pingDestination(expiryTimestamp, 80000000000, 80000000000, 0, chainId, payloads, addresses);
}
```

_pingDestination function calls the requestToDest function on the Router's Gateway contract to generate a cross-chain request and stores the nonce returned into the lastEventIdentifier. 

```sh
    function _pingDestination(
        uint64 expiryTimestamp,
        uint64 destGasPrice,
        uint64 ackGasPrice,
        uint64 chainType,
        string memory chainId,
        bytes[] memory payloads,
        bytes[] memory addresses
    ) internal {
        gatewayContract.requestToDest(
            expiryTimestamp,
            false,
            Utils.AckType.ACK_ON_SUCCESS,
            Utils.AckGasParams(ackGasLimit, ackGasPrice),
            Utils.DestinationChainParams(
                destGasLimit,
                destGasPrice,
                chainType,
                chainId
            ),
            Utils.ContractCalls(payloads, addresses)
        );
    }
```
# `Handling a crosschain request`

To handle cross-chain requests on the destination chain we make use of handleRequestFromSource function. This function is automatically called when the destination chain receives the requests, and the user does not need to manually call it. The core logic of the dapp is written in this function to store the message and contacts in two mappings, map and contacts, respectively.

Mappings:

map: This mapping is used to store the messages between two contacts. The messages are stored in a string array indexed by the two addresses of the contacts who are communicating.

contacts: This mapping is used to store the contacts for a particular user. It maps the address of a user to an array of addresses of their contacts.

handleRequestFromSource() Function
The handleRequestFromSource function is called automatically by the destination chain upon receipt of a cross-chain request. It takes in the following parameters:

srcContractAddress: The address of the source contract.
payload: The payload of the cross-chain request, which contains the relevant information to be processed by the destination chain.
srcChainId: The ID of the source chain.
srcChainType: The type of the source chain.
The function decodes the payload and retrieves the information, which includes a request ID, two wallet addresses (user0 and user1), and a message. If the message is a special "#NULL#" value, the addresses are added to each other's contacts in the contacts mapping. If the message is not "#NULL#", it is added to the message history in the map mapping between the two addresses.

getMessages() Function
The getMessages function returns the message history between two addresses. It takes in two parameters:

u0: The first wallet address.
u1: The second wallet address.
The function returns the message history stored in the map mapping between the two addresses.

getContacts() Function
The getContacts function returns the contacts of a given wallet address. It takes in one parameter:

u0: The wallet address.
The function returns the contacts stored in the contacts mapping for the given wallet address.

```sh
  mapping(address => mapping(address => string[])) public map;
    mapping(address => address[]) public contacts;

   function handleRequestFromSource(
        bytes memory srcContractAddress,
        bytes memory payload,
        string memory srcChainId,
        uint64 srcChainType
    ) external override returns (bytes memory) {
        require(msg.sender == address(gatewayContract));

        (
            uint64 requestId,
            address user0,
            address user1,
            string memory message
        ) = abi.decode(payload, (uint64, address, address, string));

        if (
            keccak256(abi.encodePacked(message)) ==
            keccak256(abi.encodePacked("#NULL#"))
        ) {
            contacts[user0].push(user1);
            contacts[user1].push(user0);
        } else {
            map[user0][user1].push(message);
            map[user1][user0].push(message);
        }

        return abi.encode(requestId, user0, user1, message);
    }

    function getMessages(
        address u0,
        address u1
    ) public view returns (string[] memory) {
        return map[u0][u1];
    }

    function getContacts(address u0) public view returns (address[] memory) {
        return contacts[u0];
    }
 ```
 
 # `Deployments`
 
To implement cross-chain communication, we need to deploy the same contract on all chains that we want to have communication with. The first step is to open https://remix.ethereum.org/ and compile the code. After that, we need to deploy the contract on the desired chains and pass in the Gateway address, destination gas limit, and source gas limit as parameters. The Gateway address can be found on the Router Protocol's Supported Chains documentation page at https://devnet-docs.routerprotocol.com/networks/supported-chains.

To determine the gas limits, we can use a gas estimator. One option is to use the hardhat-gas-reporter plugin. Alternatively, we can view recent transactions on the desired chain through its explorer to determine the gas limit.

# `Back to Frontend`

To allow for cross-chain communication, the contract must be deployed on multiple chains. After deployment, copy the ABI of the contract, the addresses of the deployed contracts, and the chain IDs. Pass this information to the runContractFunction function as follows:

```sh
const options = {
    abi: <insert ABI here>,
    contractAddress: <insert source contract address here>,
    functionName: "pingDestination",
    params: {
      chainId: <insert destination chain ID here>,
      destinationContractAddress: <insert destination contract address here>,
      user0: account,
      user1: channelReceiptAddress,
      message: "#NULL#",
    },
};

const result = await runContractFunction({ params: options });

```
 
 

