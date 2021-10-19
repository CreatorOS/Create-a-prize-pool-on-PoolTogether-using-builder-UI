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
 - Paste the recovery phrase of your testing metamask account in HDWALLET_MNEMONIC. - Same for infura api key and etherscan api key

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

![][/learn_src/learn_assets/1.png]

Copy the address of PoolWithMultipleWinnersBuilders deployed contract from deployment logs.
It's probably the last the one as shown below:

![][/learn_src/learn_assets/2.png]

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

![](/learn_src/learn_assets/3.png)

Now start the server:

```
    yarn dev
```

Open up http://localhost:3000 in your browser.

Next, we will create a prize pool using this builder.

## Creating prize pool using Builder UI

This builder allows us to create a Prize Pool with a multiple winners prize strategy. This strategy allows prize awarding periodicallyt to multiple randomly selected winners.

The first option is to select a Deposit Token and a yield source.

![][/learn_src/learn_assets/4.png]

We will select $DAI as the token and Compound as the yield source. What this means is we will allow users to deposit DAI token in the prize pool.
In turn our pool will submit those DAI tokens to Compound to generate interest which will be awarded to the winners/winner based on the prize strategy.

![][/learn_src/learn_assets/5.png]

Next up we have Pool Ticket.
Tickets give you chance to win.
When you go to pooltogether.com and deposit the tokens, tickets are what you get in exchange.

Just leave the ticket name and symbol as defaults.

In advanced settings, we have sponsorship ticket.
It's same as pool tickets except you are not eligible to win prizes.
They are purely for contributing to the yeild.
This is what PoolTogether uses to boost up the prize pools when they're low.
Leave those as defaults as well.

![][/learn_src/learn_assets/6.png]

Next we have Random Number Generator.
It's a swappable service provided by PoolTogether.
Randomness is very crucial in this case.
We want a deterministic source of entropy which is not biasable by people.
Or else people will game the system.

Let's just select Blockhash to keep it simple.

![][/learn_src/learn_assets/7.jpg]

After that we have Prize Period.
Let's select a prize period which very short say 1 minute so that we can easily play around with it.
Set it as 0.00069 which is 1 minute in days.

![][/learn_src/learn_assets/8.png]

Next up we have Number of Winners.
Let's just have 1 winner for this prize pool. We can select multiple winners as well.

If you want to distribute some percentage of prize to a static address before awarding winners, you can configure it in the Prize Split section.
We will leave it as it is.

![][/learn_src/learn_assets/9.png]

Finally we have Fairness.
This configuration allows the pool owner to prevent people from depositing money and withdrawing right away.
We have early exit fee which decays in time.
So let's say we have 1% early exit fee and if you deposit 100 DAI and want to exit right away then you have to pay 1 DAI.
But let's say the fee decay time is 14 days and if you wait for that much amount of time you can withdraw losslessly with zero fees.

Let's keep early exit fee 1% and fee decay time of 0.00208333 which is almost 3 minutes in days just for testing purposes.

Click on 'Create prize pool' button once you are done configuring the prize pool.

It will ask for confirmation in your metamask wallet. Make sure you have some rinkeby test ether in your wallet.

Kudos! Finally we have created our own custom prize pool.

Copy the New Prize Pool's contract address just for reference.

Next up we will interact with the custom prize pool we just created.

## Interacting with our custom Prize Pool

Click on 'View pool in Community UI' button to start interacting with the Prize Pool.

This will open up [https://community.pooltogether.com](https://community.pooltogether.com) with our prize pool preselected.

![][/learn_src/learn_assets/10.png]

You will see the timer for the prize pool that we set as 1 minute. It might have already run out of time.

Click on 'Account Balance' now.

Under Prize Pool Info, you will observe we have zero deposits in the pool.

![][/learn_src/learn_assets/11.png]

Let's deposit some DAI tokens.
If you don't already have DAI tokens then get some ethers from a rinkeby faucet and head over to [https://app.uniswap.org](https://app.uniswap.org) and swap those Ethers for DAI tokens.

While swapping for DAI tokens on Uniswap make sure the DAI token's address is 0x5592EC0cfb4dbc12D3aB100b257153436a1f0FEa There are many versions of DAI tokens on rinkeby testnet but we need this tokens.

Once you have correct DAI tokens it will show up under Prize Pool info as well. In the above screenshot it is 345 DAI.

![][/learn_src/learn_assets/12.png]

Now click on 'Approve DAI'.
If you have done the basics of ethereum course you might remember we need to approve tokens before a third party can swap it. This is exactly what we are doing here.

Once approved, PoolTogether will be able to deposit the amount you enter in the pool.

Let's deposit 50 DAI tokens. Enter 50 and click on 'Deposit'.

Now, check the Prize Pool Info you will see the amount we just deposited 50 DAI. Hurray!

![][/learn_src/learn_assets/13.png]

If you refresh the page, you will see we some prize money as well.

![][/learn_src/learn_assets/14.png]

## Depositing and withdrawing

Let's try to withdraw instantly. If you remember we set 3 minutes as Exit Fee Decay Time.

If 3 minutes have not passed already and if you enter the max withdraw amount by clicking on 50.00 DAI written above DAI. You will see below warning.

![][/learn_src/learn_assets/15.png]

Click 'Withdraw anyway and pay ... DAI'.

The amount you paid in exit fee gets credited to the prize amount.

![][/learn_src/learn_assets/16.png]

But if you were to wait for 3 minutes you will get your full deposited amount back without any penalty.

## Starting the award

Before we see how to start the award, let's first deposit 50 DAI in the pool again.

Once done, click on 'Prize Details' link at the bottom.
And click on 'Add PTccDAI to metamask'

![][/learn_src/learn_assets/17.png]

Now if you check Assests section in the metamask wallet.
You will see the 50 PTccDAI tokens.
Those are the tickets or tokens you got for depositing 50 DAI tokens.

Now, let's start the award process.

![][/learn_src/learn_assets/18.png]

After few minutes you will be able to click 'Complete award' button as well.

Once you do that, check your deposits. It went up by the Prize amount.

![][/learn_src/learn_assets/19.png]

In this case it went up by 0.25 DAI. That means we won the lottery.

Ofcourse, that is only because we are the only ones who deposited money in the pool.

## What's next

Now, here's a challenge for you.
Try depositing DAI tokens from two separate addresses and see who wins.
