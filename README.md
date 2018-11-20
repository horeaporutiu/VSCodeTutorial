# Use IBM Blockchain Platform extension in VSCode to develop a Smart Contract

In this tutorial, we will learn the process of using IBM Blockchain Platform's VSCode extension 
to  streamline the 
process of developing, testing, and deploying a smart contract. Once we finish the 
tutorial, we will understand how to quickly develop, demo, and deploy blockchain
application on a local Hyperledger Fabric network using VSCode. This tutorial assumes some
basic understanding of Hyperledger Fabric.

# Watch the Video

[![](docs/thumbnail.png)](https://www.youtube.com/watch?v=r77p-8k4Mpk)

### Prerequisites

- [Node v8.x or greater and npm v5.x or greater](https://nodejs.org/en/download/)
- [Yeoman (yo) v2.x](http://yeoman.io/)
- [Docker version v17.06.2-ce or greater](https://www.docker.com/get-docker)
- [Docker Compose v1.14.0 or greater](https://docs.docker.com/compose/install/)
- [VSCode 1.28.2 or higher](https://code.visualstudio.com/download)

If you are using Windows, you must also ensure the following:
- Your version of Windows supports Hyper-V and Docker:
  - Windows 10 Enterprise, Pro, or Education with 1607 Anniversary Update or later
- Docker for Windows is configured to use Linux containers (this is the default)
- You have installed the C++ Build Tools for Windows from [windows-build-tools](https://github.com/felixrieseberg/windows-build-tools#windows-build-tools)
- You have installed OpenSSL v1.0.2 from [Win32 OpenSSL](http://slproweb.com/products/Win32OpenSSL.html)
  - Install the normal version, not the version marked as "light"
  - Install the Win32 version into `C:\OpenSSL-Win32` on 32-bit systems
  - Install the Win64 version into `C:\OpenSSL-Win64` on 64-bit systems

You can check your installed versions by running the following commands from a terminal:
- `node --version`
- `npm --version`
- `yo --version`
- `docker --version`
- `docker-compose --version`

### Learning Objectives
- Install the IBM Blockchain Platform VSCode Extension
- Create a new JavaScript smart contract
- Package a smart contract
- Create, explore and understand a Hyperledger Fabric Network
- Deploy the smart contract on a local Hyperledger Fabric instance
- Use Node.js SDK to interact with deployed Smart Contract package

### Estimated time
- After the prerequisites are installed, this should take <b>approximately 30-45 minutes</b> to complete.

# Steps

1. [Create a New Smart Contract Project](#step-1-create-a-new-smart-contract-project)
2. [Modify Chaincode](#step-2-modify-chaincode)
3. [Package Smart Contract](#step-3-package-smart-contract)
4. [Install Smart Contract](#step-4-install-smart-contract)
5. [Instantiate Smart Contract](#step-5-instantiate-smart-contract)
6. [Import Certificate and Key](#step-6-import-certificate-and-key)
7. [Add Identity](#step-7-add-identity)
8. [Update Network Ports](#step-8-update-network-ports)
9. [Invoke Smart Contract](#step-9-invoke-smart-contract)
10. [Conclusion](#step-10-conclusion)

## Let's get started
![packageFile](/docs/installExtension.gif)
First thing we need to do is to install the IBM Blockchain Platform VSCode extension. You 
will need to install the latest version of VSCode to do this. To see if you have the latest 
VSCode extension, go to `Code` -> `Check for Updates`. If VSCode crashes at this point 
(which it did for me), it likely means you don't have the latest version. If so, 
update your VSCode, and once you're done, click on `extensions` on the side bar on the left 
part of your screen. At the top, search the extension marketplace for 
`IBM Blockchain Platform`. Click on `Install`. Then click on `reload`. Nice, you 
are all set to use the extension!

## Step 1. Create a New Smart Contract Project
![packageFile](/docs/createSmartContract.gif)

To create a smart contract project: 

1. Click on your newly downloaded IBM Blockchain Platform extension. It should be the extension
all the way at the bottom of the left side bar.
2. Next, use the keyboard shortcut `Shift` + `CMD` + `P` to 
bring up the command pallete. Choose **IBM Blockchain Platform: Create Smart Contract Project** from the dropdown.
3. Click **JavaScript** from the dropdown. 
4. Click **New Folder**, and name the project what you want. I named mine `demoContract`.
5. Click **Create** and then **Open** your new folder which you just created. Next, from the dropdown, click **Add to Workspace**.
6. Once the extension is done packaging your contract, you can open the `lib/my-contract.js` file to see your smart 
contract code scaffold. Nice job!

## Step 2. Modify Chaincode 
![packageFile](/docs/addChaincode.gif)
Inside your `lib/my-contract.js` file, go ahead and copy 
and paste this code: 

```
'use strict';

const { Contract } = require('fabric-contract-api');

class MyContract extends Contract {

  //update ledger with a greeting to show that the function was called
  async instantiate(ctx) {
    let greeting = { text: 'Instantiate was called!' };
    await ctx.stub.putState('GREETING', Buffer.from(JSON.stringify(greeting)));
  }

  //take argument and create a greeting object to be updated to the ledger
  async transaction1(ctx, arg1) {
    console.info('transaction1', arg1);
    let greeting = { text: arg1 };
    await ctx.stub.putState('GREETING', Buffer.from(JSON.stringify(greeting)));
    return JSON.stringify(greeting);
  }

}

module.exports = MyContract;
```

<!-- **Please note:** the gifs may not match the following function, but this is the one 
you should have in your `lib/my-contract.js` file now!  -->

Let's examine the functions we defined. The `instantiate` function creates a greeting 
object and then stores that on the ledger with the key `GREETING`. 
The `transaction1` function takes the Hyperledger
Fabric context, and one argument, `arg1` which is used to store a greeting as defined by the user.
The `ctx.stub.putState` method is used to record the `greeting` on the ledger and then we 
return that object back. Save the file, and proceed!

## Step 3. Package Smart Contract
![packageFile](/docs/packageSmartContract.gif)
Now that we have created our smart contract and understood which functions we defined,
it's time to package it so we can install it on a peer.

1. Open the command pallete with `Shift` + `CMD` + `P` and select **package smart contract**
2. In the left sidebar, click on the IBM Blockchain Platform icon (it looks like a square). 
On the top-left corner, you will have all of your smart contract packages. You should 
see `demoContract@0.0.1` if everything went well. 

## Step 4. Install Smart Contract
![packageFile](/docs/installSmartContract.gif)

Ok, we're more than halfway there. Now for the fun part! Let's install this contract on the peer!
To do this, we must first connect to a Hyperledger Fabric network. The network that comes with
the VSCode extension is perfect for development - it offers the minimal resources to develop and 
test your contract.

The following Docker containers are started on your local machine, each with a different role in
the network: Orderer, Certificate Authority, CouchDB, and Peer. 

To start our network, look at your IBM Blockchain Platform extension, at the bottom-right corner
where it says `Blockchain Connections`. 

1. You should see `local_fabric`. Go ahead and click that.
That should automatically run a script and you should see the output as follows: 
```
Starting fabricvscodelocalfabric_orderer.example.com_1 ... done
Starting fabricvscodelocalfabric_ca.example.com_1      ... done
Starting fabricvscodelocalfabric_couchdb_1             ... done
Starting fabricvscodelocalfabric_peer0.org1.example.com_1 ... done
``` 

2. Click the `local_fabric` connection again. Now that it's up and running, it should 
take you to your channel view, which should show one channel, named `mychannel`. Click on 
`mychannel`.
3. This will expand your channel and show the peers and smart contracts. Click on 
`peers` and you should see `peer0.org1.example.com`. Right-click on that peer, and click on
`Install Smart Contract`.
4. The extension will ask you which package to install, so choose `demoContract@0.0.1`. That's it! Nice job!

## Step 5. Instantiate Smart Contract
![packageFile](/docs/instantiateSmartContract.gif)

This is the real test - will our smart contract instantiate properly? Let's see.
1. From the IBM Blockchain extension, in the bottom-left corner, under `Blockchain Connections`,
right-click on `mychannel` and then on `Instantiate / Upgrade Smart Contract`.
2. The extension will then ask you which contract and version to instantiate - choose `demoContract@0.0.1`.
3. The extension will then ask you which function to call - type in `instantiate`.
4. Next, it will ask you the arguments, for which there are none, so just hit enter.

The extension will do some work, and then you should see in the bottom-right corner that the contract
was successfully instantiated. Hooray!!

## Step 6. Import certificate and key
![packageFile](/docs/gitPull.gif)

At this point, we need to start interacting a bit more closely with our
Fabric instance. We'll need to prove to the certificate authority that we are allowed
to create a digital identity on the network, and that is by showing the certificate
authority our certificate and private key. For the sake of keeping this tutorial as short as possible,
I have a GitHub repo with all the certs, keys, and scripts we 
will work with, so go ahead and git clone this repo in a directory of your choice, but outside
the `demoContract` directory.

1. In a terminal, navigate to a directory to clone the GitHub repository into.

2. Clone the GitHub repository by using the following command:

  ```
  $ git clone https://github.com/horeaporutiu/VSCodeLocalNetwork.git
  ```

3. Import this folder into your VSCode workspace by right-clicking an empty space
under your smart contract directory in VSCode and selecting **Add folder to workspace**. 
Find the recently clone folder `VSCodeLocalNetwork` and double-click it.
4. Skim the `cert` and `key` files - they contain our certificate and private key
that will be used to request an identity from the certificate authority. There is 
also a `addIdentity.js` script which has the following code: 

```
'use strict';

// Bring key classes into scope, most importantly Fabric SDK network class
const fs = require('fs');
const { FileSystemWallet, X509WalletMixin } = require('fabric-network');

// A wallet stores a collection of identities for use
const wallet = new FileSystemWallet('./_idwallet');

async function main(){

    // Main try/catch block
    try {

        // define the identity to use
        const cert = fs.readFileSync('./cert').toString();
        const key = fs.readFileSync('./key').toString();
        const identityLabel = 'User1@org1.example.com';

        // prep wallet and test it at the same time
        await wallet.import(identityLabel, X509WalletMixin.createIdentity('Org1MSP', cert, key));

    } catch (error) {
        console.log(`Error adding to wallet. ${error}`);
        console.log(error.stack);
    }
}

main().then(()=>{
    console.log('done');
}).catch((e)=>{
    console.log(e);
    console.log(e.stack);
    process.exit(-1);
});
```

This code creates an identity by passing in our certificate and private key.
Notice we are using the `fabric-network` NPM package to call the `createIdentity` method, 
and then using the `import` function to add that identity to our wallet. The most important line of this file
is the `await wallet.import(identityLabel, X509WalletMixin.createIdentity('Org1MSP', cert, key));` line
which actually creates a new MSP identity using our cert and key file. This [MSP(Membership Service Provider)]
(https://hyperledger-fabric.readthedocs.io/en/release-1.3/msp.html) identity will be able to connect to the 
network and invoke smart contracts.

## Step 7. Add Identity
![packageFile](/docs/addIdentityScript.gif)
Now that we have an identity that can interact with our network, we need to 
to run `npm install` to install all the dependencies that are needed to connect to our local Fabric network. 

1. Run `npm install` in the `VSCodeLocalNetwork`.
2. Then, run `node addIdentity`. You should see that this command creates a new folder called `_idwallet`
 and populates that folder with the MSP identity, which in our case goes by the name of
`User1@org1.example.com`. Nice job! 

## Step 8. Update network ports
![packageFile](/docs/addPorts.gif)

1. Next, open the `network.yaml` in the `VSCodeLocalNetwork` folder. We will use this file to connect 
to our Docker containers running locally.

2. To see your docker containers running locally, run `docker ps`. Your output should look something like this: 

![packageFile](/docs/ports.png)

**Important Note: your ports will differ from those shown** 

3. Next, look for `fabric-peer`,`fabric-ca`, and `fabric-orderer` in the `docker ps` logs.
It's likely going to be hard to read, so be careful here. The 5 digit number is the port 
number that we need to update in the appropriate place in the `network.yaml` file.
Update the peer, ca, and orderer's url field as shown in the preceding gif.
 
In the example above the ports in `network.yaml` would be set as follows:

```
orderers:
  orderer.example.com:
    url: grpc://localhost:32823
```
Then the peer: 

```
peers:
  peer0.org1.example.com:
    # this URL is used to send endorsement and query requests
    url: grpc://localhost:32827

```

Then the CA: 

```
certificateAuthorities:
  ca-org1:
    url: http://localhost:32822
```
4. Great job. Save the file.

## Step 9. Invoke Smart Contract
![packageFile](/docs/invoke.gif)

Ok, so we've instantiated our contract, created our identity, so now what?
Well now, let's actually invoke the functions in our contract! To do this, 
we will need to invoke a script from our `VSCodeLocalNetwork` directory. 

1. From our git clone, we should have a file called `invoke.js`. Let's examine this file.

2. After we create an instance of the `fabric-network`, we connect to the network with the following code:
```
await gateway.connect(connectionProfile, connectionOptions);
```

Notice here that the connection profile is the `network.yaml` file that we updated in the previous step, and the `connectionOptions` is an object which contains the credentials from our `_idwallet` directory. 

3. After we connect to the network, we need to specify the channel to connect to, which 
in our case happens to be `mychannel`. This line connects to our channel:

```
const network = await gateway.getNetwork('mychannel');
```

4. Our channel may have many contracts installed, so in the next line, we specify which contract
to invoke. In our case, this is `demoContract`. 

```
const contract = await network.getContract('demoContract');
```

5. The final part of our script picks which function to invoke, and specifies the arguments. In 
our case we are invoking `transaction1` with an arg of `hello` as can be seen
here: 

```
let response = await contract.submitTransaction('transaction1', 'hello');
```

6. Now, we can run the script, with this command:

```
$ node invoke.js
```
If all goes well, you should see the following output:

```
VSCodeLocalNetwork$ node invoke.js
Connected to Fabric gateway.
Connected to mychannel.

Submit hello world transaction.
info: [TransactionEventHandler]: _strategySuccess: strategy success for transaction "9bf00460721a9d42dfe0d3bf93151f10be8b0a57011d4b24262ef03d5a33ee5e"

{ text: 'hello' }
Disconnect from Fabric gateway.
done
```

Woo!! That's it! Nice job!


## Step 10. Conclusion
Nice job! You are done! You learned how to create, package, install, instantiate, 
and invoke a smart contract using Hyperledger's newest API's. At this point, 
you can focus on developing your smart contract, and update your `my-contract.js`
file knowing that you have taken care of the networking aspects of blockchain, 
and that you can successfully invoke, and update your ledger using just VSCode,
Node.js, and Docker. Please, please, please reach out to me if there are bugs,
comment on this post, and tell me, and I will fix them. Thanks so much for 
reading this post, and hope you enjoyed it! Horea Blockchain out!

# Links

* [IBM Blockchain - Marbles demo](https://github.com/IBM-Blockchain/marbles)
* [Hyperledger Fabric Docs](https://hyperledger-fabric.readthedocs.io/en/release-1.2/)


# Learn more

* **Blockchain Code Patterns**: Enjoyed this Code Pattern? Check out our other [Blockchain Code Patterns](https://developer.ibm.com/code/technologies/blockchain/)

* **Blockchain 101**: Learn why IBM believes that blockchain can transform businesses, industries – and even the world. [Blockchain 101](https://developer.ibm.com/code/technologies/blockchain/)

# License
[Apache 2.0](LICENSE) 
