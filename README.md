# Use IBM Blockchain Platform extension in VSCode to develop a Smart Contract

In tutorial, we will learn the process of using IBM Blockchain Platform's VSCode extension 
to  streamline the 
process of developing, testing, and deploying a smart contract. The audience for this 
tutorial is a developer that has some basic understanding of blockchain, and wants to 
understand how to deploy a network to a Hyperledger fabric instance. Once you finish the 
tutorial, you will understand how to quickly develop, demo, and deploy your blockchain
application on a multi-organization network using VSCode. 

### Prerequisites

You will need the following installed in order to use the extension:
- [Node v8.x or greater and npm v5.x or greater](https://nodejs.org/en/download/)
- [Yeoman (yo) v2.x](http://yeoman.io/)
- [Docker version v17.06.2-ce or greater](https://www.docker.com/get-docker)
- [Docker Compose v1.14.0 or greater](https://docs.docker.com/compose/install/)

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

### Let's get started
![packageFile](/docs/installExtension.gif)
First thing we need to do is to install the IBM Blockchain Platform VSCode extension. You will
need to latest version of VSCode to do this. To see if you have the latest VSCode extension, go to
`Code` -> `Check for Updates`. If VSCode crashes at this point (which it did for me), it likely
means you don't have the latest version. Check the troubleshooting section below if your VSCode
crashed. Otherwise, update your VSCode, and once you're done, click on `extensions` on the 
side bar on the left part of your screen. At the top, search the extension marketplace for 
`IBM Blockchain Platform`. Click on `Install`. Then click on
`reload`. Nice, you are all set to use the extension! 🙌🏼

## 1. Create a new smart contract project
![packageFile](/docs/createSmartContract.gif)
To create a smart contract project, click on your newly
downloaded IBM Blockchain Platform extension. It should be the extension all the way at the bottom of the left side bar. Next, use the keyboard shortcut `Shift` + `CMD` + `P` to 
bring up all of the commands for the IBM Blockchain Platform. Choose 
`Create Smart Contract Project` from the dropdown. Next,
click `JavaScript` from the dropdown. Next, click `New Folder`, and name the project what you want. I named mine
`demoContract`. Click `Create` and then `Open` your new folder which you just created. Next, you'll be prompted with a dropdown, and you can click `Add to Workspace`. Once the extension is done packaging your
contract, you can open the `lib/my-contract.js` file to see your smart 
contract code scaffold. Nice job! 👏🏼

## 2. Modify Chaincode 
![packageFile](/docs/addChaincodeCursor.gif)
Inside your `lib/my-contract.js` file, go ahead and copy 
and paste this code: 

```
'use strict';

const { Contract } = require('fabric-contract-api');

class MyContract extends Contract {

    async instantiate(ctx) {
      let greeting = { text: 'Hi' };
      await ctx.stub.putState('GREETING', Buffer.from(JSON.stringify(greeting)));
    }

    async transaction1(ctx, arg1) {
        console.info('transaction1', arg1);
        let greeting = { text: arg1 };
        await ctx.stub.putState('GREETING', Buffer.from(JSON.stringify(greeting)));
        return greeting;
    }

}

module.exports = MyContract;
```

It's not much, but it will the 
`await ctx.stub.putState('GREETING', Buffer.from
(JSON.stringify(greeting)));` line will update the ledger
with the key-value pair of 
```
{
  "GREETING": {
    "text": "hi" 
  }
}
```
Not much going on here, since this is a hello-world 
tutorial, but it shows up the basics of updating the 
ledger, which is the most important part of being a 
blockchain developer. Also, in `transaction1` you will see that we 
are actually returning an object, so we will be able to see if 
our smart contract is invoked successfully since we can inspect the
object that is returned from this function. Save the file, and proceed! Good job! 🔥

## 3. Package Smart Contract
![packageFile](/docs/packageSmartContract.gif)
Now that we have created our smart contract, it's time to package it so we can 
connect to a Hyperledger Fabric instance. Let's do our favorite keyboard shortcut 
again, `Shift` + `CMD` + `P` and then look for `package smart contract`, and click 
on that. The extension will do the rest for you, so sit back, relax, and drink some ☕️.
In the left sidebar, click on the IBM Blockchain Platform icon. This click will take you
into another dimension - the blockchain dimension. On the top-left corner, you will 
have all of your smart contract packages. You should see `demoContract@0.0.1` if 
everything went well. Nice job! Time to install, and instantiate this contract! 🙌🏼

## 4. Install Smart Contract
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

## 5. Instantiate Smart Contract
![packageFile](/docs/instantiateSmartContract.gif)
This is the real test - will our smart contract instantiate properly? Let's see.
From the IBM Blockchain extension, in the bottom-left corner, under `Blockchain Connections`,
right-click on `mychannel` and then on `Instantiate / Upgrade Smart Contract`. The extension
will then ask you which contract and version to instantiate, and we will pick `demoContract@0.0.1`. 
The extension will then ask you which function to call. You can go ahead and type in `instantiate`.
Next, it will ask you the arguments, for which there are none, so just hit enter. The extension will
do some work, and then you should see in the bottom-right corner that the contract was successfully
instantiated. Hooray!! 🤟🏼

## 6. Import certificate and key
<!-- ![packageFile](/docs/createCertsAndScript.gif) -->
At this point, we need to start interacting a bit more closely with our
Fabric instance. We'll need to import some certs to prove to the certificate
authority that we are allowed to create a digital identity on the network, and 
that is by showing the certificate authority our certificate and private key. For the sake of keeping this tutorial as short as possible,
I have made a GitHub repo with all the certs, keys, and scripts we 
will work with, so go ahead and git clone this repo in your current
workspace. So go ahead and go outside `demoContract` to clone this 
directory - we want to keep our smart contract directory as 
lightweight as possible. Then go ahead and clone my repository, and 
then do a `ls` command inside the network to see what files we have.
```
$ git clone https://github.com/horeaporutiu/VSCodeLocalNetwork.git
$ cd VSCodeLocalNetwork
$ ls
```
You'll see the cert and key files which are certificates that will
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
in a folder called `_idwallet`.



## 7. Update network ports
![packageFile](/docs/networkPorts.gif)
Next, let's create our network file which will specify which ports our 
docker containers are running on. Create a new file called `network.yaml` in 
the `fabricNetwork` directory. Copy and paste the following in the file: 

```
---
#
# The network connection profile provides client applications the information about the target
# blockchain network that are necessary for the applications to interact with it. These are all
# knowledge that must be acquired from out-of-band sources. This file provides such a source.
#
name: "basic-network"

#
# Any properties with an "x-" prefix will be treated as application-specific, exactly like how naming
# in HTTP headers or swagger properties work. The SDK will simply ignore these fields and leave
# them for the applications to process. This is a mechanism for different components of an application
# to exchange information that are not part of the standard schema described below. In particular,
# the "x-type" property with the "hlfv1" value example below is used by Hyperledger Composer to
# determine the type of Fabric networks (v0.6 vs. v1.0) it needs to work with.
#
x-type: "hlfv1"

#
# Describe what the target network is/does.
#
description: "The basic network"

#
# Schema version of the content. Used by the SDK to apply the corresponding parsing rules.
#
version: "1.0"

#
# [Optional]. But most apps would have this section so that channel objects can be constructed
# based on the content below. If an app is creating channels, then it likely will not need this
# section.
#
channels:
  # name of the channel
  mychannel:
    # Required. list of orderers designated by the application to use for transactions on this
    # channel. This list can be a result of access control ("org1" can only access "ordererA"), or
    # operational decisions to share loads from applications among the orderers.  The values must
    # be "names" of orgs defined under "organizations/peers"
    orderers:
      - orderer.example.com

    # Required. list of peers from participating orgs
    peers:
      peer0.org1.example.com:
        # [Optional]. will this peer be sent transaction proposals for endorsement? The peer must
        # have the chaincode installed. The app can also use this property to decide which peers
        # to send the chaincode install request. Default: true
        endorsingPeer: true

        # [Optional]. will this peer be sent query proposals? The peer must have the chaincode
        # installed. The app can also use this property to decide which peers to send the
        # chaincode install request. Default: true
        chaincodeQuery: true

        # [Optional]. will this peer be sent query proposals that do not require chaincodes, like
        # queryBlock(), queryTransaction(), etc. Default: true
        ledgerQuery: true

        # [Optional]. will this peer be the target of the SDK's listener registration? All peers can
        # produce events but the app typically only needs to connect to one to listen to events.
        # Default: true
        eventSource: true

#
# list of participating organizations in this network
#
organizations:
  Org1:
    mspid: Org1MSP

    peers:
      - peer0.org1.example.com

    # [Optional]. Certificate Authorities issue certificates for identification purposes in a Fabric based
    # network. Typically certificates provisioning is done in a separate process outside of the
    # runtime network. Fabric-CA is a special certificate authority that provides a REST APIs for
    # dynamic certificate management (enroll, revoke, re-enroll). The following section is only for
    # Fabric-CA servers.
    certificateAuthorities:
      - ca-org1

#
# List of orderers to send transaction and channel create/update requests to. For the time
# being only one orderer is needed. If more than one is defined, which one get used by the
# SDK is implementation specific. Consult each SDK's documentation for its handling of orderers.
#
orderers:
  orderer.example.com:
    url: grpc://localhost:32823

    # these are standard properties defined by the gRPC library
    # they will be passed in as-is to gRPC client constructor
    grpcOptions:
      ssl-target-name-override: orderer.example.com

#
# List of peers to send various requests to, including endorsement, query
# and event listener registration.
#
peers:
  peer0.org1.example.com:
    # this URL is used to send endorsement and query requests
    url: grpc://localhost:32827

    grpcOptions:
      ssl-target-name-override: peer0.org1.example.com
      request-timeout: 120001

# Fabric-CA is a special kind of Certificate Authority provided by Hyperledger Fabric which allows
# certificate management to be done via REST APIs. Application may choose to use a standard
# Certificate Authority instead of Fabric-CA, in which case this section would not be specified.
#
certificateAuthorities:
  ca-org1:
    url: http://localhost:32822
    # the properties specified under this object are passed to the 'http' client verbatim when
    # making the request to the Fabric-CA server
    httpOptions:
      verify: false

    # Fabric-CA supports dynamic user enrollment via REST APIs. A "root" user, a.k.a registrar, is
    # needed to enroll and invoke new users.
    registrar:
      - enrollId: admin
        enrollSecret: adminpw
    # [Optional] The optional name of the CA.
    caName: ca-org1

```
Next, let's run `docker ps` to see which ports our containers are running on. 
🚧🚧🚧 Everyone's ports will be different, so you cannot just copy my port numbers   
here, it wont work! 🚧🚧🚧

Your output should look something like this: 

![packageFile](/docs/ports.png)


It's gonna be jumbled up, probably, so be careful here. We need to update 
our `network.yaml` file in 3 places: the peer, orderer, and CA port. So look 
for the first port number you see with those docker containers. Note that 
you'll have a dev-peer container but you do not want to use that port. Just 
the regular peer. So for the example above, I would change the ports in 
`network.yaml` to the following. First the orderer: 

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

## 8. Invoke Smart Contract
Ok, so we've instantiated our contract, created our identity, so now what?
Well now, let's actually invoke it! To do this, we will need a script.
So in your `demoContract`
directory, create a new file called, uhh... `invoke.js`. Let's go with that.
Inside that file, paste the following code: 

```
'use strict';

const yaml = require('js-yaml');
const { FileSystemWallet, Gateway } = require('fabric-network');
const fs = require('fs');

// A wallet stores a collection of identities for use
const wallet = new FileSystemWallet('./_idwallet');

async function main() {

  // A gateway defines the peers used to access Fabric networks
  const gateway = new Gateway();

  // Main try/catch block
  try {
    const identityLabel = 'User1@org1.example.com';
    let connectionProfile = yaml.safeLoad(fs.readFileSync('.network.yaml', 'utf8'));

    let connectionOptions = {
      identity: identityLabel,
      wallet: wallet
    };

    // Connect to gateway using application specified parameters
    await gateway.connect(connectionProfile, connectionOptions);

    console.log('Connected to Fabric gateway.');

    // Get addressability to PaperNet network
    const network = await gateway.getNetwork('mychannel');

    console.log('Connected to mychannel. ');

    // Get addressability to commercial paper contract
    const contract = await network.getContract('demoContract');

    console.log('\nSubmit commercial paper issue transaction.');

    // issue commercial paper
    let response = await contract.submitTransaction('transaction1', 'hello');
    console.log('Response from invoking smart contract: ')
    console.log(response);
    return response;

  } catch (error) {
    console.log(`Error processing transaction. ${error}`);
    console.log(error.stack);
  } finally {
    // Disconnect from the gateway
    console.log('Disconnect from Fabric gateway.');
    gateway.disconnect();
  }
}

// invoke the main function, can catch any error that might escape
main().then(() => {
  console.log('done');
}).catch((e) => {
  console.log('Final error checking.......');
  console.log(e);
  console.log(e.stack);
  process.exit(-1);
});
```

Hope you learned something, and if you find anything missing, encounter a nasty bug, error, etc. please open an issue on this repo above⤴⤴⤴⤴⤴! 🕷🕷🕷
























<!-- If you check your downloaded files now, you should have a file starting with `cred` ending in `.json`. Let's rename this file to `creds.json` and move this file to the root of our carauction-network directory.

Open `enrolladmin.js` and the newly downloaded `creds.json` in an editor of your choice. I prefer VSCode. -->












<!-- [![Deploy to IBM Cloud](https://bluemix.net/deploy/button.png)](https://bluemix.net/deploy?repository=https://github.com/IBM/watson-second-opinion) -->
# Links

* [IBM Blockchain - Marbles demo](https://github.com/IBM-Blockchain/marbles)
* [Hyperledger Fabric Docs](https://hyperledger-fabric.readthedocs.io/en/release-1.2/)


# Learn more

* **Blockchain Code Patterns**: Enjoyed this Code Pattern? Check out our other [Blockchain Code Patterns](https://developer.ibm.com/code/technologies/blockchain/)

* **Blockchain 101**: Learn why IBM believes that blockchain can transform businesses, industries – and even the world. [Blockchain 101](https://developer.ibm.com/code/technologies/blockchain/)

# License
[Apache 2.0](LICENSE) -->
<!-- 

at this point, you can go into your terminal and run `$ docker ps` to see the containers. You should
have 4 at this point, namely, the peer, orderer, CA, and couchdb. Nice job :) -->

<!-- First, let's create a seperate folder for our identity work,
since we want to keep our contract directory as lightweight as possible.
Create a new folder called `fabricNetwork`. Go into your newly created directory
and with the following command: 
```
$ cd fabricNetwork
```
Then clone my files into that fabricNetwork directory:
```
$ cd fabricNetwork
```

Now, we'll create a few files that will hold certificates that will 
be used to grant us a digital identity. First create a new file 
called `cert` and paste in the following code in there:
```
-----BEGIN CERTIFICATE-----
MIICGTCCAb+gAwIBAgIQPyhm+v0ZIqCo6MATzLc+5jAKBggqhkjOPQQDAjBzMQsw
CQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNU2FuIEZy
YW5jaXNjbzEZMBcGA1UEChMQb3JnMS5leGFtcGxlLmNvbTEcMBoGA1UEAxMTY2Eu
b3JnMS5leGFtcGxlLmNvbTAeFw0xNzA4MzEwOTE0MzJaFw0yNzA4MjkwOTE0MzJa
MFsxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1T
YW4gRnJhbmNpc2NvMR8wHQYDVQQDDBZVc2VyMUBvcmcxLmV4YW1wbGUuY29tMFkw
EwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEL/SomNVO3R5nnsemQ4im/UUZ8Ixs7/nH
3NH1ROfVJ+m7niDf1ZmhvTyiJzrUpI+n5+/OKIX/Z/VhDuAIR/QLLKNNMEswDgYD
VR0PAQH/BAQDAgeAMAwGA1UdEwEB/wQCMAAwKwYDVR0jBCQwIoAgQjmqDc122u64
ugzacBhR0UUE0xqtGy3d26xqVzZeSXwwCgYIKoZIzj0EAwIDSAAwRQIhAJk63AHS
CEvJh64Yx5CnWDgDYNoj0jsi+gGheIxbUYgMAiAi/qPG7KEuuDBL4LlZRfkeatMW
ZKPD7ikt+vOYgVnqlA==
-----END CERTIFICATE-----

```

Save the file! Similarly, create another file called `key`, and paste in the following private key:
```
-----BEGIN PRIVATE KEY-----
MIGHAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBG0wawIBAQQgJ8IrEgxfZzjGsyt+
0o27jvhwUJE2W1PrFeZi8LwHbiuhRANCAAQv9KiY1U7dHmeex6ZDiKb9RRnwjGzv
+cfc0fVE59Un6bueIN/VmaG9PKInOtSkj6fn784ohf9n9WEO4AhH9Ass
-----END PRIVATE KEY-----

```
And save the file! 
Next, create a file called `addIdentity.js` and paste in the following
code: 

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
Save the file. Great. We're doing amazing so far. Only a few more things and you'll 
be ready to invoke that smart contract! In terms of the code above, 
we are importing the new `fabric-network` module from NPM, and then 
using that to create an identity by passing in our cert and private 
key that we created in the previous steps. The new identity will be stored
in a folder called `_idwallet`.

## 6. Create Identity
![packageFile](/docs/addIdentity.gif)
Now that we have our certs set up, we are ready to create our digital
identity. Let's create a new file called `addIdentity.js` and paste in the
following code:

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
and then save that file. Next, run `npm init`, and just hit enter so you
get all the defaults. Next, we need to install the dependency that our script
uses, and we will use npm for that. Run the following to save the dependency in 
your `package.json`:

`npm install fabric-network --save`

You may have some vulnerabilities in your packages, like me. If so,
run `npm audit fix`.

Now, run the script:

`node addIdentity.js`. This should have created a folder called `_idwallet`
and saved certs and keys in there. Nice, we are sooo close! -->