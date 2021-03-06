---
id: faq
title: Frequently Asked Questions
sidebar_label: Frequently asked questions
---
## Joining the Testnet / Making Deposits

#### How can I make a deposit?

The best way to make a deposit and join the testnet is to follow the direct instructions on https://prylabs.net/participate. We have more detailed instructions in our documentation portal depending on your architecture [here](https://docs.prylabs.network/docs/install/lin/activating-a-validator/).

#### My deposit on prylabs.net isn’t working / metamask isn’t working, help!

Ensure your metamask is set to the Goerli Network and NOT mainnet. If metamask isn’t working, we recommend trying the Portis wallet instead which is also enabled in prylabs.net. If you are getting a “funded too recently” error from our faucet, then please reach out to us on discord [here](https://discord.gg/CTYGPUJ) for more goerli ETH.

#### How can I get a bunch of test ETH to spin up a lot of validators?

Our current faucet only gives out increments of 32.5 Goerli test ETH at any given time and prevents sending out to the same address too often. If you need a larger amount of test ETH, please reach out to us on discord [here](https://discord.gg/CTYGPUJ).

#### Can I get back my testnet ETH? How can I withdraw my validator gains?

Deposits into eth2 are one-way in the beginning. Functionality of withdrawing gains transferring ETH won’t be available until later phases of the project. You can read more about the phases of eth2 [here](https://docs.ethhub.io/ethereum-roadmap/ethereum-2.0/eth-2.0-phases/).

## Running a Node

#### How can I view the metrics of my node? How can I visualize them?

Your node is running the popular [prometheus](https://prometheus.io/) server for metrics collection which displays its data by default on localhost:8080/metrics. You can visualize these metrics by connecting them to a [grafana](https://grafana.com/) instance.

#### Can I run a node in the background?

Absolutely! If you are running via docker, it is as easy as adding the -d flag to your docker run command itself to run it in “detached” mode. You can see your running docker instances by typing in `docker ps`. If you are running via our precompiled binaries, such as the prysm.sh script, you can use tools such as `systemd` (example [here](https://paulgorman.org/technical/blog/20171121184114.html))

#### I got slashed! Why? How can I avoid getting slashed?

Slashings in the network occur when you propose two conflicting blocks (blocks with the same slot but different inner data) or when you do a conflicting vote or a surround vote (read more about surrounding [here](https://blog.ethereum.org/2020/01/13/validated-staking-on-eth2-1-incentives/)). These will never happen if your node is operating as intended. You can, however, get yourself slashed if you run multiple validator clients with the same keys. There is a chance they may create two blocks or two conflicting votes at the same time as each other, causing you to get slashed from eth2. This is more common than it seems, as typically developers want to run redundant instances of servers for reliability purposes (if one goes down, the other one is ready right away to do its job). Famously, this same event happened in the Cosmos network as seen [here](https://twitter.com/zmanian/status/1145072296723275776?lang=en). There are some cool projects that aim to prevent validators from getting slashed such as [this one](https://devpost.com/software/eth-2-0-validator-protection) that are community built, but we have yet to standardize this functionality into Prysm.

#### Where can I read more about the incentive model in eth2?

[This resource](https://blog.ethereum.org/2020/01/13/validated-staking-on-eth2-1-incentives/) from the Ethereum blog is an excellent example.

#### My CPU/RAM usage is huge, what’s going on?

Memory management is an ongoing process for our team - we are constantly working on improving the experience of running a node itself. For now, these are the specs we recommend for our current testnet (these values will go down significantly over time):

Recommended Hardware
Processor: Intel Core i7–4770 or AMD FX-8310 or better
Memory: 8GB RAM
Storage: 100GB available space SSD
Internet: Broadband connection

Minimum Hardware:
Operating System: 64-bit Linux, Mac OS X, Windows
Processor: Intel Core i5–760 or AMD FX-8100 or better
Memory: 4GB RAM
Storage: 20GB available space SSD
Internet: Broadband connection

If that still does not help, please file an issue with our team on Github [here](https://github.com/prysmaticlabs/prysm/issues/new?template=bug_report.md).

#### What’s the best cloud service to run a beacon node on?

We find Digital Ocean tends to have fairly priced instances for running beacon nodes, but as always, we recommend you running a node yourself on your machine for the full experience. You can also try the small instances on AWS ec2 to run in the cloud.

#### Can I run this on a raspberry pi? What are the instructions?

Yes we currently support arm 64-bit architectures such as the raspberry pi 4 and they go out as part of our pre-compiled binary releases [here](https://github.com/prysmaticlabs/prysm/releases). Our documentation portal has instructions on how to run the entire installation process [here](https://docs.prylabs.network/docs/install/arm/).

## Managing Validators

#### How can I create a new validator key and join the testnet?

The best way to join the testnet is to follow the direct instructions on [https://prylabs.net/participate](https://prylabs.net/participate). We have more detailed instructions in our documentation portal depending on your operating system [here](https://docs.prylabs.network/docs/install/lin/activating-a-validator/).

I deposited but it’s taking a long time to become active - how can I check if my deposit was correct?

Even after you deposit, it takes several hours to join the testnet as an active validator and can take even longer if there is a queue ahead of you. You can track your validator’s status on block explorers such as https://beaconcha.in and https://beacon.etherscan.io. Please be patient, as there is quite a wait for new validators to become active.

#### How can I check on the status of my validator?

Make sure your beacon node is synced and running, and run the command
`./prysm.sh validator accounts status --keymanager={unencrypted, interop, keystore, wallet} --keymanageropts={$YOUR_PATH, $YOUR_PATH/keymanageropts.json} --beacon-rpc-provider=localhost:4000`.


#### I made a correct deposit and my validator status is still UNKNOWN, what’s going on?

There are a few possibilities. (1) your deposit has not yet been processed by beacon nodes. It takes a while for the beacon node to be able to process logs from the eth1 Goerli chain by design. If you have already waited a few hours and no luck, there is a chance that (2) your deposit did not verify (that is, you used some other method of creating the deposit than our recommended, standard way on prylabs.net), or (3) you never actually sent a deposit to the right contract address

#### How can I check my validator public key?

You can run the command `./prysm.sh validator accounts keys --keystore-path=$HOME/validator --password=YOURPASSWORD` to check your validator public keys currently within that keystore.

I’m running my node and validator and they seem fine but my balance is going down, help!

First of all, check your validator public key on https://beaconcha.in or https://beacon.etherscan.io to cross-reference that you are, indeed, losing funds as detailed by these block explorers. Note that block explorers may not always have the most up-to-date information. Also, check your node and validator for any errors (is the beacon node full of ERROR logs? is it off? Is the validator client down?). Finally, if everything seems perfect to you but you are still losing funds, please get in touch with us on our discord [here](https://discord.gg/CTYGPUJ).

#### What is balance and what is effective balance?

Your validator balance is the actual amount of ETH you have from being a validator in the testnet. It can go up or down every epoch depending on your participation. Effective balance is a bit different. It is a value that lags behind your actual balance and is used exclusively to determine rewards and penalties for your validator. It has a max value of 32ETH, but your regular balance is uncapped. You can read more about the purpose of effective balance in this excellent post by Attestant [here](https://www.attestant.io/posts/understanding-validator-effective-balance/).

#### When do my validators get to propose a block?

Validators receive new assignments approximately every 6.4 minutes. This period of time is known as an “epoch” and you may be assigned to propose a block several times in an epoch. Proposing a block, however, is rare, as there can only be one block proposer per slot and there are many validators in the network. Voting on blocks, that is, being assigned as an attester, is very common and you will be assigned to attest once per epoch if you are an active validator. You can see how many votes your validator has been assigned to create vs. how many blocks your validator has been assigned to propose by using a popular block explorer such as https://beaconcha.in and looking up your validator index or public key.

#### How can I run multiple validator keys at the same time? Do I need to spin up multiple validator clients?

A validator client looks for a keystore to read in multiple keys and use them for validating. As long as you created multiple accounts in the same keystore, you will be able to use a single validator client to run multiple keys. We’ve even been able to run 50,000 keys on a single validator!
