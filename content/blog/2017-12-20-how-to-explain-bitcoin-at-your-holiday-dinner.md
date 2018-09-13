---
title: "How to Explain Bitcoin at Your Holiday Dinner"
sub_title: "Making sense of Bitcoin and the blockchain"
tags: ["Bitcoin", "Cryptocurrency", "Blockchain", "Ethereum", "Fintech"]
date: 2017-12-20
publishdate: 2017-12-20
hero_image: /images/blog/2017-12-20-how-to-explain-bitcoin-at-your-holiday-dinner/hero.jpg
author:
  name: "John Daly"
  url: "http://johnmcdaly.com/"
  mail: "john@meshstudio.io"
  avatar: "https://avatars2.githubusercontent.com/u/1719443?s=460&v=4"
---

Bitcoin has become one of the biggest buzzwords of the year. From the mind-boggling growth, to the stories of people becoming billionaires seemingly overnight, it is a topic that is dominating headlines in both online circles and the mainstream media. While the hype and success stories are all fine and good, most people have no idea what Bitcoin is, let alone the blockchain technology that backs it.

With the holiday season in full swing, many of us will be with family and loved ones. Bitcoin will certainly be near the top of the list of potential conversation topics at the dinner table. In this post, I will hopefully make it easier for you to be able explain these concepts to your family and friends.

## First things first, what exactly is Bitcoin?
Bitcoin is what is known as a ‘cryptocurrency’; it is essentially digital cash.

## What is the motivation behind the creation of Bitcoin?
Bitcoin was created to allow for a way to make electronic payments without the need for a central authority. In other words, there is no need for a middleman like a bank or payment verification system, meaning faster transactions and fewer fees. It also protects against the risk of a central authority disappearing (a bank failing), and affecting all of its users.


| ![Network Comparison](/images/blog/2017-12-20-how-to-explain-bitcoin-at-your-holiday-dinner/network-compare.png) |
|:--:|
|Traditional online payment systems operate using a centralized network setup. The central authority being the bank or verification service. Bitcoin transactions occur over a decentralized network, which does not have a single authority.|

## Who made it?
[Satoshi Nakamoto](https://en.wikipedia.org/wiki/Satoshi_Nakamoto)

## Why does Bitcoin have value?

| ![Coinbase Chart](/images/blog/2017-12-20-how-to-explain-bitcoin-at-your-holiday-dinner/price-chart-scaled.png) |
|:--:|
| Chart from Coinbase |

Like other commodities, Bitcoin’s value is derived from what the market determines its worth to be. There is no intrinsic value to Bitcoin; after all, it is just some data on a computer. There are buyers willing to pay for it though, and it is this _faith_ that gives it value.

Ironically, it is _faith_ that gives fiat currency (e.g. US Dollar, Euro, etc.) its value as well. There isn’t any actual intrinsic value to fiat currency (i.e. no gold backing) either. Fiat currency actually derives its value from the faith that other parties will accept that currency in the future.

## Why the rapid rise in value?
There could be many reasons for that. Buyers might think that Bitcoin is the future of electronic payments (some online retailers [accept it as a form of payment](http://www.nasdaq.com/article/what-companies-accept-bitcoin-cm323438), alongside traditional currencies) and could replace currencies. They could be hopeful that the hype will take their investments to the moon, or something else entirely.

## Where does it come from?
Bitcoin is built on what is known as the __*blockchain*__. From the name, the blockchain is an ordered list of blocks. Each block is essentially a list of transactions that have occurred on the network. Each block also contains a reference to the block that precedes it in the chain.
In plain English, the blockchain is essentially a ledger system, which is shared between computers. Each computer has a copy of the blockchain, which will be updated when the computer is told by its neighbors that a new block/blocks have been added to it.

| ![Blockchain](/images/blog/2017-12-20-how-to-explain-bitcoin-at-your-holiday-dinner/blockchain.png) |
|:--:|
| A very simplified visual of a blockchain. The arrows represent the reference each block has to the block that precedes it in the chain. |

In order for someone to be able to participate in the Bitcoin network, they will have to set up their computer and install special software on it. For the technically inclined, [here are instructions](https://bitcoin.org/en/full-node#other-linux-distributions) for setting up your own computer on the Bitcoin network.

Many people build high-performance computers, solely for use on the Bitcoin network. For serious users, it can be a very expensive and time consuming process. You might be asking yourself: “Why in the world would anyone go through the trouble to do that?” or “What do they get out of doing this?”

This is where the Bitcoin currency comes in; you can earn bitcoins by connecting your computer to the network. The computers that are a part of the Bitcoin network are responsible for maintaining the blockchain through what is essentially a __ledger verification__ process. A common term for this work is ‘mining’ and the miners are awarded bitcoins for their work. We will discuss mining for bitcoin in the next section.

## How bitcoins are awarded (or created)
Let’s explore how the blockchain works in practice, and how bitcoins are created from it:

The blockchain is shared across a network of connected computers. These computers all work to verify the history of the electronic ledger, so that people feel confident in being able to use the network to pay for things. Making sure the ledger system is accurate is a difficult problem to tackle, and requires a lot of computing power from the network to accomplish. Therefore, the work that the computers are doing on the network is highly valuable. To incentivize people to do this work, the currency bitcoin is used as a reward.

Okay, so what has to happen for someone to get bitcoin as a reward? Let’s lay it out in steps, and then go through each one:

1. Transactions are created on the network
2. Transactions are put into a ‘block’
3. Once a block is full of transactions, it is ready to be ‘mined’
4. Once a block is successfully mined, it is added to the blockchain, and the computer that solved it is awarded bitcoin for its work

## Transactions
Here is how an example of a transaction, and how it would work on the network:

Person A wants to pay Person B for something. To do this, they need to let the network know that this is happening. To start this process, they announce to their neighbors (computers that they are connected to in the network) that they are making this payment. Each neighbor will relay the announcement to their own neighbors. Eventually the announcement will make its way to the entire network:

| ![Blockchain Transaction](/images/blog/2017-12-20-how-to-explain-bitcoin-at-your-holiday-dinner/blockchain-transaction.png) |
|:--:|
| Person A announces their transaction to the neighboring computers that they are connected to. Each of those computers do the same. Eventually, the transaction announcement will make its way to every connected computer in the network. |

In order for the network to verify that Person A is actually the one announcing the transaction, public-key cryptography is needed. This is a whole other topic in itself (and is absolutely crucial for privacy on the Internet, so it is definitely worth learning about), so I will try and keep things simple; but if you want a quick overview, you can learn more here.

In short, with public-key cryptography a user has two keys: a public and private key. They can give the public key away to anyone, but they must keep the private key a secret. Put simply, the private key is used to scramble data, but in a way where the data can be unscrambled with the public key. The process of scrambling or encrypting the data is referred to as signing. This technique is used on the Bitcoin network, so that computers can verify that the person making the transaction is actually who they say they are.

When Person A makes a transaction, they use their private key to scramble (sign) some of the data. They also include the public key with the transaction. If the computers in the network can take this public key and unscramble the other data, then they can be sure that it was scrambled by Person A. If the public key doesn’t work, then the transaction will be identified as invalid and ignored.

## Blocks
As transactions are announced and verified, they are put into a ‘block’. Once a block is full of transactions (a full block is 1 MB), it is ready to be processed (mined) and added to the blockchain.

This is where things get slightly more complicated; Bitcoin was designed in a way where the number of bitcoins is limited. Because of this, there needs to be significant work done to mine a block to get the bitcoin. The limited supply and work required to mine a block helps to prevent inflation (you can read why bitcoin is considered a deflationary currency here, and you can see how the inflation rate goes down over time here).

The creator(s) of Bitcoin devised a system which will allow for blocks to be successfully mined every 10 minutes or so; this is accomplished through a difficult math problem, which is created using data about the transactions in the block, along with the address of the most recent block on the blockchain.

So mining for bitcoin is basically having a computer attempt to solve a very difficult math problem, which in turn ensures that the validity of the blockchain remains intact. The math problem is setup so that it is easy to verify a correct answer, but very difficult to actually arrive at that answer.

It essentially comes down to computers rapidly guessing and checking solutions. If a computer solves the problem, it tells the rest of the network (through its neighbors, like how transactions are announced), the block is added to the chain, and bitcoins are awarded to the computer that solved the block. Once the solved block is added to the chain, the ledger will reflect that Person A has paid Person B the money, and the amount of money in A and B’s accounts will be updated accordingly.

On average, it takes around 10 minutes for a block to be successfully ‘mined’. If you have ever tried to transfer money from one bank account to another, you may have noticed it can take a long time as the bank verifies the transfer. The blockchain allows for these transactions to occur much more quickly.

## What about fraud within the network?
Since the blockchain is operated on a decentralized network, there is no central authority making sure that all the computers are playing fair. Obviously, this means that fraud needs to be a primary concern. Once again, the complicated math problems play a role.

Lets consider an example, where a fraudster wants to change the amount of money they paid for something. This will require them to go back to the block where that transaction took place. Due to the nature of the blockchain, and the cryptographic math problems involved, they will also need to process each of the blocks following the one that their transaction fell into. The good computers only focus on the latest block in the chain, so they will simply outpace anyone trying to modify earlier transactions.

| ![Bitcoin Fraud Protection](/images/blog/2017-12-20-how-to-explain-bitcoin-at-your-holiday-dinner/bitcoin-fraud-protection.jpg) |
|:--:|
| A great illustration from Mark Montgomery of IEEE, showing why this type of fraud is not likely to occur |

What about the potential for a user to create a transaction where their account suddenly has a bunch of Bitcoin in it? This is not possible, as the network is aware of which coins have been created through the mining process; coins that do not have valid digital signatures will be ignored. So it is not possible for users to create Bitcoins out of thin air.

## How is privacy achieved?
Obviously, people value privacy when paying for things and transferring money. In a system with no central authority to keep your information private, how do people keep their identities a secret? With the way the network is set up for Bitcoin, it is more or less totally transparent; you can see all the transactions made between accounts; with every transaction being permanently recorded in the blockchain. I have heard many people say that Bitcoin is ‘untraceable’; it’s possible to make it hard to trace where money is going, but the system is still fully transparent, so it is not untraceable by any means. In order to provide some privacy, users can generate an address for Bitcoins to be sent for payment; this address will be publicly visible, but it doesn’t necessarily reveal who owns the account. It is recommended that you generate a new address for every payment that you receive, so as to prevent people from snooping on the activity on a single address.

## What does the future look like for Bitcoin and blockchain?
Are Bitcoin and other cryptocurrencies the future of money? Or are they a bubble that will rival [tulip mania](https://bitcoin.org/en/full-node#other-linux-distributions)? Honestly, no one knows. The concept of the blockchain is very powerful, and is being studied as a possible platform to build from. There are already many applications that run on blockchain platforms; one of the more famous examples is [CryptoKitties](https://bitcoin.org/en/full-node#other-linux-distributions), which runs on the [Ethereum](https://www.ethereum.org/) network. Regardless of what happens with the price of cryptocurrencies, the blockchain has big potential, and it will be exciting to see what people with build with it.

Happy Holidays!

For more reading on Bitcoin and the blockchain, here are some good resources to start with:

- [‘Bitcoin: A Peer-to-Peer Electronic Cash System’](http://bitcoin.org/bitcoin.pdf)
- [‘An Introduction to Ethereum and Smart Contracts: Bitcoin & The Blockchain’](https://auth0.com/blog/an-introduction-to-ethereum-and-smart-contracts/)
- [‘What is Bitcoin Mining?’](https://www.bitcoinmining.com/#wibm)