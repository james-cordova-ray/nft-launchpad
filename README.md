# Moon Rockets
## Proprietary Crypto Token 

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

In this project we will be creating a crypto token from scratch using Solidity to code the smart contract and then deploy to local testnet and Ropsten testnet.
## Project Features

- Transfer token to any wallet address on Ethereum
- Random rewards for buyers and holders
- login feature to access the platform using Decentralized ID

## Tech
To accomplish this we will leverage the following tools and technologies:
- [React.JS] - for the Frontend to interact with our smart contract
- [Hardhat.JS] which is a tool kit built for smart contract development and deployment.
- [Ethers.JS] and [Web3.JS] are usefull libraries we will be leveraging to communicate with our smart contract aswell as with MetaMask.

# Development from scratch

### Create React App
```sh
npx create-react-app client
cd client
```
### Dependencies
>NodeJS [Node.js](https://nodejs.org/) v10+ to run.
>Ethers
>Hardhat
>@nomiclabs/hardhat-waffle
>chai for testing
>@nomiclabs/hardhat-ethers

```sh
npm install ethers hardhat @nomiclabs/hardhat-waffle chai @nomiclabs/hardhat-ethers
```
### ERC-20 Token
https://docs.openzeppelin.com/contracts/2.x/api/token/erc20
```sh
npm install ethers hardhat @nomiclabs/hardhat-waffle chai @nomiclabs/hardhat-ethers
```

### Hardhat Ethereum development tooling
Basic configuration for Ethereum development. Creates a basic smart contract, deployment script and some more useful goodies right out the box.

```sh
npx hardhat
>"create a sample project"
>"default for project root"
>"do you want to add a gitgnore ? Y"
```
This will create three folders in our project root; contracts, hardhat.config.js and scripts

#### hardhat.config.js
This is what we get right out the box. We will need to configure a few things in our "module.export" now
```sh
require("@nomiclabs/hardhat-waffle");

// This is a sample Hardhat task. To learn how to create your own go to
// https://hardhat.org/guides/create-task.html
task("accounts", "Prints the list of accounts", async (taskArgs, hre) => {
  const accounts = await hre.ethers.getSigners();

  for (const account of accounts) {
    console.log(account.address);
  }
});

// You need to export an object to set up your config
// Go to https://hardhat.org/config/ to learn more

/**
 * @type import('hardhat/config').HardhatUserConfig
 */
module.exports = {
  solidity: "0.8.4",
};

```
#### ABI's
When our project is compiled, hardhat will collect all our smart contracts and convert them into machine readable code called ABI's. To interact with our smart contract after deployment, we will need to reference the ABI for this particular smart contract. For that reason we need to configure hardhat to place these ABI's in src folder. 

```sh
module.exports = {
  solidity: "0.8.4",
  paths: {
      artifacts: './src/artifacts'
  }
};
```
From the project root folder run :
```sh
npx hardhat compile
```

This will compile and place the ABI in Artifacts folder within src.


## Deployment config
Hardhat will compile our project and deploy it to a network of our choice. This could be local testnet, Ropsten testnet or Ethereum Mainnet. Whatever you choose needs to be configured in module.exports under paths. hardhat will setup a local testnet. 
#### Hardhat - local blockchain
```sh
module.exports = {
  solidity: "0.8.4",
  paths: {
      artifacts: './src/artifacts'
  },
  networks:{
      hardhat:{
          hardhat:'1337'
      }
  }
};
```

#### Public Testnet - Ropsten
Setup a node on Infura and run a full Ropsten test node. Configure hardhat.config.js to connect to this new Infura Ropsten node. Next configure metamask wallet to connect to Ropsten. Create a new wallet address which is local to Ropsten blockchain and export the private key. Place this private key in hardhat config and store your private key as an environment variable. 
```sh
//from the same directory as react app root
vi ~/.zshrc
export="[privatekey]"
source ~/.zshrc
```


```sh
module.exports = {
  solidity: "0.8.4",
  paths: {
      artifacts: './src/artifacts'
  },
  networks:{
      ropsten:{
      url: "",
      accounts:[`0x${process.env.private_key}`]
    }
  }
};
```

#### Public Mainnet - Ethereum
Setup a node on Infura and run a full Ropsten test node. Configure hardhat.config.js to connect to this new Infura Ropsten node. Next configure metamask wallet to connect to Ropsten. Create a new wallet address which is local to Ropsten blockchain and export the private key. Place this private key in hardhat config.

```sh
module.exports = {
  solidity: "0.8.4",
  paths: {
      artifacts: './src/artifacts'
  },
  networks:{
      mainnet:{
      url: "",
       accounts:[`0x${process.env.private_key}`]
    }
  }
};
```


#### Deployment script
We will use hardhat to reference the contract first and then run deploy method to deploy contract to a network of our choice.
Any transaction on the network cost a fee which is called "Gas". So to pay for these fees we will also be connecting to a wallet that holds enough Ether. 

```sh
const hre = require("hardhat");

async function main() {
  // Hardhat always runs the compile task when running scripts with its command
  // line interface.
  //
  // If this script is run directly using `node` you may want to call compile
  // manually to make sure everything is compiled
  // await hre.run('compile');

  // We get the contract to deploy
  const Token = await hre.ethers.getContractFactory("Token");
  const token = await Token.deploy();

  await token.deployed();

  console.log("Token deployed to", token.address);
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

### deployment
deploy smart contract to local testnet
```sh
npx hardhat run scripts/deploy.js --network localhost
```
deploy to Ropsten
```sh
npx hardhat run scripts/deploy.js --network ropsten
```

### Blockchain Network Node
##### local blockchain - hardhat
run a blockchain on localhost
```sh
npx hardhat node
```
You will now see 20 accounts created on this new blockchain and 10,000 ETH allocated for each account. 

##### Ropsten blockchain Network 
Setup a Ropsten blockchain network node 
```sh
npx hardhat run scripts/deploy.js --network localhost
```



## Docker

Dillinger is very easy to install and deploy in a Docker container.

By default, the Docker will expose port 8080, so change this within the
Dockerfile if necessary. When ready, simply use the Dockerfile to
build the image.

```sh
cd dillinger
docker build -t <youruser>/dillinger:${package.json.version} .
```

This will create the dillinger image and pull in the necessary dependencies.
Be sure to swap out `${package.json.version}` with the actual
version of Dillinger.

Once done, run the Docker image and map the port to whatever you wish on
your host. In this example, we simply map port 8000 of the host to
port 8080 of the Docker (or whatever port was exposed in the Dockerfile):

```sh
docker run -d -p 8000:8080 --restart=always --cap-add=SYS_ADMIN --name=dillinger <youruser>/dillinger:${package.json.version}
```

> Note: `--capt-add=SYS-ADMIN` is required for PDF rendering.

Verify the deployment by navigating to your server address in
your preferred browser.

```sh
127.0.0.1:8000
```



**Free Software, Hell Yeah!**

[//]: # (These are reference links used in the body of this note and get stripped out when the markdown processor does its job. There is no need to format nicely because it shouldn't be seen. Thanks SO - http://stackoverflow.com/questions/4823468/store-comments-in-markdown-syntax)

   [dill]: <https://github.com/joemccann/dillinger>
   [git-repo-url]: <https://github.com/joemccann/dillinger.git>
   [john gruber]: <http://daringfireball.net>
   [df1]: <http://daringfireball.net/projects/markdown/>
   [markdown-it]: <https://github.com/markdown-it/markdown-it>
   [Ace Editor]: <http://ace.ajax.org>
   [node.js]: <http://nodejs.org>
   [Twitter Bootstrap]: <http://twitter.github.com/bootstrap/>
   [jQuery]: <http://jquery.com>
   [@tjholowaychuk]: <http://twitter.com/tjholowaychuk>
   [express]: <http://expressjs.com>
   [AngularJS]: <http://angularjs.org>
   [Gulp]: <http://gulpjs.com>

   [PlDb]: <https://github.com/joemccann/dillinger/tree/master/plugins/dropbox/README.md>
   [PlGh]: <https://github.com/joemccann/dillinger/tree/master/plugins/github/README.md>
   [PlGd]: <https://github.com/joemccann/dillinger/tree/master/plugins/googledrive/README.md>
   [PlOd]: <https://github.com/joemccann/dillinger/tree/master/plugins/onedrive/README.md>
   [PlMe]: <https://github.com/joemccann/dillinger/tree/master/plugins/medium/README.md>
   [PlGa]: <https://github.com/RahulHP/dillinger/blob/master/plugins/googleanalytics/README.md>
