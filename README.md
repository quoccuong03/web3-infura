# Smart Contracts using Web3.js and Infura
`Web3.js` is a collection of libraries which allow you to interact with a local or remote Ethereum node using an HTTP or IPC connection. In this exercise, we will use `web3.js` to deploy a contract on the Ethereum Ropsten testnet and play with it. We will first compile a contract, deploy it and finally call some of the contract’s functions. In order for web3 to connect to the Ropsten testnet, we will use Infura.io API as our provider.

## Requirements
* Node 	v13.5.0
* NPM 	v6.13.4
* Solc 	v0.6.4
* Web3 	v1.2.6

## Installation
To start with, create a new project on your preferred directory.<br/>
In the directory, open a terminal and initialize a `NodeJS` project:
```sh
$ npm init –y
```
After that, install `solc-js`:
```sh
$ npm install solc@0.6.4
```

Last but not least, `web3.js`:
```sh
$ npm install web3@1.2.6
```

## Retrieving Infura Keys
Now let’s get the necessary [Infura.io](https://infura.io/) provider.

Change the endpoint from `MAINNET` to `ROPSTEN`. Then, copy the HTTPS Endpoint.

Go back to your project, then create an app.js file with the Infura endpoint as the provider. A provider abstracts a connection to the Ethereum blockchain for issuing queries and sending state changing transactions. It is simply a connection to the network so that you don’t need to download the whole blockchain data if you want to interact it.
 
## Compiling the Smart Contract
The Smart Contract object is a meta-class. What this means is that functions in it are not defined until it is instantiated with an `application binary interface (ABI)` which is usually generated by a compiler, such as the Solidity compiler. `solc` is used to generate JavaScript bindings for the Solidity compiler.
Now, implement an `ArrayOfFacts` smart contract which stores a string array of facts in the blockchain. `Only the owner` of the contract can `add facts`, but anyone can `count` how many facts there are and get a fact by `index`.

Now that the smart contract has been implemented, go back to _app.js_. Read the contract and store it in a variable.

Compile the contract.
Implement the `standardCompilerInput` which contains compilation instructions for the `solc` compiler. To learn more, see: https://solidity.readthedocs.io/en/v0.5.0/using-the-compiler.html#compiler-input-a’nd-output-json-description.<br/>
Then, compile the contract via `solc.compile()` and access the object that we are interested in.
 
Open a terminal on your project folder and run the following command, then examine the output:
```sh
$ node app.js
```
 
## Deploy a Smart Contract
Now that we have compiled our _ArrayOfFacts.sol_ smart contract, it is time to deploy it on the Ropsten Testnet. Retrieve one private key from MetaMask and use it as a signer for the deployment transaction. This account will also be the contract owner.
  		 
If you do not have ETH, use the MetaMask faucet: https://faucet.metamask.io/ 
Export the `private key` (`DO NOT USE` an account that holds actual mainnet ETH).
 		 
Save the exported `privateKey` in your _app.js_ file. Don’t forget to prefix the copied key with `0x`.
 
To deploy a contract to the Ethereum Network, two things are required:
*	The contract’s bytecode
*	The contract’s application binary interface (ABI)

Both are generated from the Solidity compilation process. We already have this from an earlier step and this is stored in our `compiledContract` variable.
Import the account in `web3` using the private key retrieved from an earlier step (_see Line 37_).
Then, use `web3.eth.Contract(jsonInterface, [address], [options])`, which will initialize a contract with all its methods defined in the json interface object. 
Since this is a deployment operation, we do not have an existing contract address of an already deployed contract to interact with yet. Leave this parameter as `null`.

Then, add the following as additional options:
*	`data` – the bytecode of the contract
*	`gas` – the maximum gas provided for a transaction (gas limit)
*	`from` – the address from which the transaction should be made. 

Finally, call the initialized contract’s `deploy()` function.<br/>
If everything goes well, the promise resolves with a new contract instance and sends the transaction. When the deployment is successful, that means the transaction is mined. The `callback` function will print the contract address.
 
Run _app.js_ and you’ll get the deployed contract address.
```
$ node app.js
Contract created at 0x<DEPLOYED-CONTRACT-ADDRESS>
```
 
Verify that the contract has indeed been deployed at the Ropsten Test Network by visiting:
```
https://ropsten.etherscan.io/address/<DEPLOYED-CONTRACT-ADDRESS>
```
 
 
## Adding Facts
Now that the Smart Contract is deployed, we will connect to it using `web3` to add facts to our contract.
For ease of use, reuse _app.js_, but `remove the deploy code` and assign the contract address to a variable.
 
Invoke the `add()` method of the Smart Contract. First, initialize the contract instance. Since the contract is now deployed, we need the `ABI` and the `contract address`.
 
Run app.js:
```sh
$ node app.js
```
The status shows whether the `addFact()` transaction was successful or not.
Copy the `transactionHash` and examine the transaction more in in Etherscan:
```
https://ropsten.etherscan.io/tx/<TRANSACTION-HASH>
```
 
Great! Now you have interacted with the smart contract and added a new fact.

Now, what will happen if the someone who is not the contract owner tries to `add()` a fact to the contract? _RESULT: `FAIL`_<br/>
Get another address, which has any ETH, export the new private key, and execute the `add()` method.

Run app.js:
```sh
$ node app.js
```
We expect this transaction to _fail_ as it was executed using a `non-owner` account.
The transaction has been reverted and we see that the status flag is false to indicate that this transaction did not succeed. Take a look at Etherscan to see more transaction details:
```
https://ropsten.etherscan.io/tx/<TRANSACTION-HASH>
```
 
## Retrieving Information
Implement the interaction which calls the contract’s `getFact(uint256 index)` and `count()` functions.<br/>
Keep in mind that when you want to GET something from a contract, you do not need a wallet! Therefore, only the ABI and contract address are needed. (_See Line 60_).

Since you do not need a wallet to retrieve information, this also means that retrieving information from a contract is `free`.
Comment out the previous `addFact()` code and replace it with the `getFact` function:
```sh
$ node app.js
Fact 1: China Grapples With Mystery Pneumonia-Like Illness 01/06/2020
Total recorded facts: 1
```
## Module
MI4: Module: E4
