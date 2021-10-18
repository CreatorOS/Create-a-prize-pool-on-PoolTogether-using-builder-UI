# Create a prize pool on PoolTogether using builder UI

In the last quest, we saw how to setup PoolTogether contracts on local system using Hardhat.
In this quest, we will setup PoolTogether contracts on rinkeby testnet. 
After that, we will setup PoolTogether Builder UI to interact with those contracts.
We will create our own custom Pool or pot where users can deposit tokens and a single user will be awarded a reward.


## Deploying PoolTogether contracts on rinkeby testnet

Before we start setting up and deploying contract, first create accounts and grab api keys from Infura and Etherscan. We will use Infura to deploy our smart contracts to the testnets. 

Just go to [infura](https://infura.io) and sign up. 

After that, select "Ethereum" tab and click on "Create a Project" button.

Once you create the project, copy the Project ID from the settings menu and keep it handy.

Now, create an account on [etherscan](https://etherscan.io) as well and grab the api keys from there.

Also, grab the recovery phrase of your testing metamask account. 
Hardhat will use the first account generated from that recovery phrase to deploy the contract to rinkeby. 
So make sure you have enough rinkeby testnet ether in your account.

Now, cd inside the contracts directory.

Copy over .envrc.example to .envrc

```
    cp .envrc.example .envrc
```

Open up .envrc  
    - Paste the recovery phrase of your testing metamask account in HDWALLET_MNEMONIC.
    - Same for infura api key and etherscan api key

You need to install [direnv](https://direnv.net/docs/installation.html) before moving forward.

Now enable the env vars using [direnv](https://direnv.net/docs/installation.html)
```
   direnv allow 
```

Now, fire up these commands to install the dependencies if you haven't already and deploy the contracts to rinkeby testnet:
```
    yarn
    yarn deploy rinkeby
```

You can go to [https://rinkeby.etherscan.io](https://rinkeby.etherscan.io) and paste your wallet address there.
In the transactions section you will see all the contracts that were deployed with just one command.
[][./learn_src/learn_assets/1.png]

Copy the address of PoolWithMultipleWinnersBuilders deployed contract from deployment logs. 
It's probably the last the one as shown below:
[][./learn_src/learn_assets/2.png]


## Setup PoolTogether Builder UI

PoolTogether Builder is a frontend that allows us to interact with on-chain factories. 

The contracts that we deployed contains these factories or builders which allows us to spin up as many prize pools as we want.

We will see what exactly is a prize pool and what different attributes we can configure in the coming quest.

Let's setup Builder UI now.

```
    cd ..
    git clone git@github.com:pooltogether/pooltogether-pool-builder-ui.git
    cd pooltogether-pool-builder-ui
    yarn
```

Now copy over the .envrc.example

```
    cp .envrc.example .envrc
```

Remove all <> values from keys so that we don't get syntax error.

Update the infura id with id we grab earlier.

Also, change the network name key to `rinkeby`.

Allow direnv now:
```
    direnv allow
```

Update the `lib/constants.js` file with the PoolWithMultipleWinnersBuilders address we grabed earlier like shown below:

[][./learn_src/learn_assets/2.png]

Now start the server:
```
    yarn dev
```



