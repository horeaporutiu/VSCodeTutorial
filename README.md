# Use IBM Blockchain Platform extension in VSCode to develop a Smart Contract

In tutorial, we will learn the process of using IBM Blockchain Platform's VSCode extension 
to  streamline the 
process of developing, testing, and deploying a smart contract. The audience for this 
tutorial is a developer that has some basic understanding of blockchain, and wants to 
understand how to deploy a network to a Hyperledger Fabric instance. Once you finish the 
tutorial, you will understand how to quickly develop, demo, and deploy your blockchain
application on a local Hyperledger Fabric network using VSCode. 

To find the video equivalent of this tutorial, please go here: https://www.youtube.com/watch?v=r77p-8k4Mpk&t=2s

### Prerequisites

You will need the following installed in order to use the extension:
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
- Get, install, and troubleshoot your IBM Blockchain Platform VSCode Extension
- Create a new smart contract project using Node.js with the VSCode Extension
- Explore, understand, and package the smart contract
- Deploy the smart contract on both IBM Cloud and local Hyperledger Fabric instance
- Use SDK to interact with deployed Smart Contract package

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
VSCode extension, go to
`Code` -> `Check for Updates`. If VSCode crashes at this point (which it did for me), it likely
means you don't have the latest version. Check the troubleshooting section below if your VSCode
crashed. Otherwise, update your VSCode, and once you're done, click on `extensions` on the 
side bar on the left part of your screen. At the top, search the extension marketplace for 
`IBM Blockchain Platform`. Click on `Install`. Then click on
`reload`. Nice, you are all set to use the extension! 🙌🏼

## Step 1. Create a New Smart Contract Project
![packageFile](/docs/createSmartContract.gif)
To create a smart contract project, click on your newly
downloaded IBM Blockchain Platform extension. It should be the extension all the way at the bottom of the left side bar. Next, use the keyboard shortcut `Shift` + `CMD` + `P` to 
bring up all of the commands for the IBM Blockchain Platform. Choose 
`Create Smart Contract Project` from the dropdown. Next,
click `JavaScript` from the dropdown. Next, click `New Folder`, and name the project what you want. I named mine
`demoContract`. Click `Create` and then `Open` your new folder which you just created. Next, you'll be prompted with a dropdown, and you can click `Add to Workspace`. Once the extension is done packaging your
contract, you can open the `lib/my-contract.js` file to see your smart 
contract code scaffold. Nice job! 👏🏼

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

The 
`await ctx.stub.putState('GREETING', Buffer.from
(JSON.stringify(greeting)));` line will update the ledger
with the `greeting` object.

🚧🚧🚧 Note: the chaincode has been updated to the following, 
but the following gifs don't reflect that - don't worry, if you have
the below chaincode, you are all good! 

```
async transaction1(ctx, arg1) {
        console.info('transaction1', arg1);
        let greeting = { text: arg1 };
        await ctx.stub.putState('GREETING', Buffer.from(JSON.stringify(greeting)));
        return JSON.stringify(greeting);
    }
```

Not much going on here, since this is a hello-world 
tutorial, but it shows up the basics of updating the 
ledger, which is the most important part of being a 
blockchain developer. Also, in `transaction1` you will see that we 
are actually returning an object, so we will be able to see if 
our smart contract is invoked successfully since we can inspect the
object that is returned from this function. Save the file, and proceed! Good job! 🔥

## Step 3. Package Smart Contract
![packageFile](/docs/packageSmartContract.gif)
Now that we have created our smart contract, it's time to package it so we can 
connect to a Hyperledger Fabric instance. Let's do our favorite keyboard shortcut 
again, `Shift` + `CMD` + `P` and then look for `package smart contract`, and click 
on that. The extension will do the rest for you, so sit back, relax, and drink some ☕️.
In the left sidebar, click on the IBM Blockchain Platform icon. This click will take you
into another dimension - the blockchain dimension. On the top-left corner, you will 
have all of your smart contract packages. You should see `demoContract@0.0.1` if 
everything went well. Nice job! Time to install, and instantiate this contract! 🙌🏼

## Step 4. Install Smart Contract
![packageFile](/docs/installSmartContract.gif)
Ok, we're more than halfway there. Now for the fun part! Let's install this contract on the peer!
To do this, we must first connect to a Hyperledger Fabric network. The network that comes with
the VSCode extension is perfect for development - it offers the minimal set up to develop, and 
test your contract. The network consists of a peer, an orderer, a certificate authority, and 
a couchdb instance, all running on seperate docker containers. To start our network, look at 
your IBM Blockchain Platform extension, at the bottom-right corner where it says `Blockchain Connections`.
You should see something that says `local_fabric`. Go ahead and click that. That should 
automatically run a script and you should see the output as follows: 
```
Starting fabricvscodelocalfabric_orderer.example.com_1 ... done
Starting fabricvscodelocalfabric_ca.example.com_1      ... done
Starting fabricvscodelocalfabric_couchdb_1             ... done
Starting fabricvscodelocalfabric_peer0.org1.example.com_1 ... done
``` 
Which shows the docker containers starting up. There is additional output too.
Nice. Now, click the `local_fabric` connection again. Now that it's up and running, it should 
take you to your channel view, which should show one channel, named `mychannel`. Click on 
`mychannel`, and it will expand the `Peers` and `Instantiated Smart Contracts`. Click on 
`peers` and you should see `peer0.org1.example.com`. Right-click on that peer, and click on
`Install Smart Contract`. Next, the extension will ask you which package, and just choose
`demoContract@0.0.1`. That's it! Nice job!

## Step 5. Instantiate Smart Contract
![packageFile](/docs/instantiateSmartContract.gif)
This is the real test - will our smart contract instantiate properly? Let's see.
From the IBM Blockchain extension, in the bottom-left corner, under `Blockchain Connections`,
right-click on `mychannel` and then on `Instantiate / Upgrade Smart Contract`. The extension
will then ask you which contract and version to instantiate, and we will pick `demoContract@0.0.1`. 
The extension will then ask you which function to call. You can go ahead and type in `instantiate`.
Next, it will ask you the arguments, for which there are none, so just hit enter. The extension will
do some work, and then you should see in the bottom-right corner that the contract was successfully
instantiated. Hooray!! 🤟🏼

## Step 6. Import certificate and key
![packageFile](/docs/gitPull.gif)
At this point, we need to start interacting a bit more closely with our
Fabric instance. We'll need to import some certs to prove to the certificate
authority that we are allowed to create a digital identity on the network, and 
that is by showing the certificate authority our certificate and private key. For the sake of keeping this tutorial as short as possible,
I have made a GitHub repo with all the certs, keys, and scripts we 
will work with, so go ahead and git clone this repo in your current
workspace. So go ahead and go outside `demoContract` to clone this 
directory - we want to keep our smart contract directory as 
lightweight as possible. To do this, just issue the following command:

```
demoContract $ cd ..
```
This will take you outside your `demoContract` directory.

Then go ahead and clone my repository, with the following command:
```
$ git clone https://github.com/horeaporutiu/VSCodeLocalNetwork.git
```
We'll need to import that folder into our workspace. To do that,
right-click on an empty space under your `demoContract` directory
and click `Add Folder to Workspace` and then find the newly cloned folder
which is called `VSCodeLocalNetwork`, and double-click it. That will 
bring our new folder into our workspace. Next, check out the
`cert` and `key` files. 
These are the certificates that will
be used to prove our identity to the certificate authority. Our main
logic comes from the `addIdentity.js` file. You'll see in that file
which should look like this:

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
In terms of the code above, 
we are importing the new `fabric-network` module from NPM, and then 
using that to create an identity by passing in our cert and private 
key that we got from the GitHub repo. The new identity will be stored
in a folder called `_idwallet`. The most important line of this file
is the `await wallet.import(identityLabel, X509WalletMixin.createIdentity('Org1MSP', cert, key));` line which actually creates a new MSP identity using our 
cert and key file. This [MSP(Membership Service Provider)](https://hyperledger-fabric.readthedocs.io/en/release-1.3/msp.html)
 identity will be able to connect to the 
network and invoke smart contracts.

## Step 7. Add Identity
![packageFile](/docs/addIdentityScript.gif)
Next, we need to run `npm install` to install all the dependencies that 
are needed to connect to our local Fabric network. 
Run `npm install` in the `VSCodeLocalNetwork`. Then, run `node addIdentity`.
You should see that this command creates a new folder called `_idwallet` and 
populates that folder with the MSP identity, which in our case goes by the name 
of
`User1@org1.example.com`. Nice job! 


## Step 8. Update network ports
![packageFile](/docs/addPorts.gif)
Next, open the `network.yaml` in the `VSCodeLocalNetwork` folder. We will use this file to connect 
to our Docker containers running locally. To see your docker containers 
running locally, run `docker ps`.

Your output should look something like this: 

![packageFile](/docs/ports.png)

 🚧🚧🚧 Everyone's ports will be different, so you  cannot just copy my port numbers 
here, it wont work! 🚧🚧🚧 Next, look for `fabric-peer`,`fabric-ca`, 
and `fabric-orderer` in the `docker ps` logs. It's gonna be jumbled up, probably, so 
be careful here. The 5 digit number is the port number that we need to update in the
appropriate place in the `network.yaml` file. So for the peer, ca, and orderer, we
must update the `url` field as shown above in the gif. 
 
So for the example above, I would change the ports in `network.yaml` to the
following. First the orderer: 

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
Nice. Save the file.

## Step 9. Invoke Smart Contract
![packageFile](/docs/invoke.gif)
Ok, so we've instantiated our contract, created our identity, so now what?
Well now, let's actually invoke it! To do this, we will need a script.
That's where our `invoke.js` comes in. Let's take a look at this file.
After importing the `fabric-network` module, we use the identity stored
in the `_idwallet` to connect to our fabric network. This happens on 
this line 
```
await gateway.connect(connectionProfile, connectionOptions);
```

Notice here that our connection profile is the `network.yaml` file that 
we updated in the previous step, and the `connectionOptions` is an object
which contains the credentials from our `./_idwallet` directory. After we 
connect to the network, we need to specify the channel to connect to, which 
in our case happens to be `mychannel`. This line connects to our channel:

```
const network = await gateway.getNetwork('mychannel');
```
Our channel may 
have many contracts installed, so in the next line, we specify which contract
to invoke. Which in our case, is `demoContract`. 

```
const contract = await network.getContract('demoContract');
```
 The final part 
of our script picks which function to invoke, and specifies the arguments. In 
our case we are invoking `transaction1` with an arg of `hello` as can be seen
here: 

```
let response = await contract.submitTransaction('transaction1', 'hello');
```

Now, we can run the script, by using this command:
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
Woo!! That's it! You made it! 💪🏼💪🏼


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
