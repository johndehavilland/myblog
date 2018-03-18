---
title: Blockchain Explained Simply
layout: post
modified: 
categories: blog
excerpt: 
tags:
- blockchain
- blockchain technology
date: '2018-03-16'
image:
  feature: 
share: true
comments: true
published: true
---

Blockchain technology is all the rage these days. You hear about it more and more yet what does it really do and how can you use it?

![Blockchain all the things]({{ site.images }}/blockchain1.jpg)
{:.post-image}

Below, I try to explain some of the fundamental concepts of blockchain. As a summary, blockchain is fundamentally made of __two concepts__:
* **a distributed ledger** - all participants in the chain has a copy of what is happening

* **cryptographic hash functions** - data can be anonymized but still verified. Changing a single thing changes the hash and invalidates the block and subsequent blocks.

It is valuable because the need for a third party has gone. Removing a third party reduces costs and time as well as improving trust and security. Think of third parties as entities like banks, auditors or even governments.

Alright, let's get into some details.

### Some History

First some history.  Let's step back about a 1000 years to the the [tiny island of Yap](https://www.npr.org/sections/money/2011/02/15/131934618/the-island-of-stone-money). There was no gold or silver on Yap, but they discovered limestone on an island several hundred miles away. They carved this limestone into large stone discs which they brought back to Yap on their boats. At some point they decided to use these stones as their money.

![Yap Stones]({{ site.images }}/yap-stones.jpg)
{:.post-image}
*Stones on the island of Yap*
{:.image-caption}

Now, these stones were heavy - often heavier than a car. So to use them as money you couldn't hand it over or move them around easily. They stayed in key spots on the island and everyone on the island memorized who owned which stone(s). When someone went to use their stone to buy something they announced it to the island. All the people on the island would update their memorized list of who owned what stone. The stone itself would stay in place but everyone on the island knew who owned it.

This is a **distributed ledger** and is the concept at the heart of Blockchain.

### Why is this so useful? 

Imagine instead of everyone knowing who owned what stone, the people of Yap decided to trust one person to maintain this information. This person would have to be trustworthy. Anytime someone wanted to make a transaction they would have to go through this person. The person may start charging a fee for this service. This is like a bank and can be thought of as a centralized ledger.

### Distributed Ledger

![The Economist blockchain cover]({{ site.images }}/blockchain-economist.jpg)
{:.post-image}
*Trust - a key value prop that blockchain brings*
{:.image-caption}

At it's core, blockchain is a distributed ledger. Instead of islanders it is now many computer nodes that have copies of this ledger. The more nodes there are then the harder it is for someone to tamper with or alter the transactions. To do so they would have to alter over 50% of the nodes. This is far harder than having to alter records controlled by a single party. 

Additionally, you no longer have to pay for a third party to act as a trusted, centralized ledger. This reduces costs and saves time. Imagine if you didn't have to go to the DMV any more.

### What is a block?

As its name implies, a blockchain is a collection of blocks. Each block contains:
* some data
* the hash of the block 
* the hash of the previous block. 

The data stored in the block varies on what the blockchain is being used for. In monetary transactions, this data could be information such as sender, receiver and amount of coins. The hash is unique and is the block's fingerprint. It is created based on the data inside so changing any of the data will cause a different hash to be created. This is useful to detect if a block has changed. Since the block also has the hash of the previous block it allows a chain to be built. 

### What is a hash function?

Its an algorithm that takes a set of input data and generates an unique output that is of fixed size. This allows you to identify the data without needing to see the original data. 

Try it out below. As you enter data, regardless of what it is, the hash generated is the same length but different (even if a single comma is different):

<div class="test-area">
  <div class="input">
          <textarea id="input" placeholder="payer=john;payee=bob;amount=10;date=20180101"></textarea>
  </div>
  <p>
    <div class="output">
      <textarea id="output" placeholder="Hash"></textarea>
    </div>
  </p>
</div>

<script>
(function($, window, document, undefined) {
  window.method = null;

  $(document).ready(function() {
    var input = $('#input');
    var output = $('#output');
    var option = $('[data-option]');

    var execute = function() {
      try {
        output.val(method(input.val(), option.val()));
      } catch(e) {
        output.val(e);
      }
    }

    function autoUpdate() {
      execute();
    }

    input.bind('input propertychange', autoUpdate);
    option.bind('input propertychange', autoUpdate);
    $('#execute').click(execute);
  });
})(jQuery, window, document);  
</script>
<script src="https://rawgit.com/emn178/js-sha3/master/build/sha3.min.js"></script>

<script>method = sha3_512;</script>

This is useful because changing a block causes it's hash to change. This then means all following blocks no longer are connected to that block and are invalidated. 

Now, without getting into all the details, when a new block is added to the chain it takes about 10 minutes (and a bunch of computing power) to get the right hash and add it. Folks who do this validation are called *miners*. A miner gets a reward if they are the one who successfully validates and adds a block to a chain. It is relatively quick to confirm if a new block is correct but takes a while to create (or mine) it. This is called Proof of Work. Due to the fact it takes a lot of computational power and time, it makes it hard for a bad actor to create fake validations. Now, if a bad actor changes data in a previous block, they would not only have perform this proof of work for that block but also for all subsequent blocks. It just wouldn't be worth it.

![Large bitcoin mine]({{ site.images }}/bitcoin_mine.jpg)
{:.post-image}
*Large bitcoin mining operation - source: [IEEE](https://spectrum.ieee.org/computing/networks/why-the-biggest-bitcoin-mines-are-in-china)*
{:.image-caption}

Now, it is important to remember that you need more than 50% of the nodes to agree. But what if the majority are bad actors? Well, this is where you still need that element of trust. Trust in that the majority of participants in your blockchain are honest and trustworthy. The more nodes, the harder it becomes to game the system.

### What about Bitcoin

![Stop thinking bitcoin, start thinking blockchain]({{ site.images }}/blockchain2.jpg)
{:.post-image}

You will often hear Bitcoin and blockchain mentioned together. It is important not to conflate the two. Bitcoin is a digital currency whose purpose is to bypass government currency controls and simplify online transactions by removing the need for a trusted third party. As explained above, this is exactly what blockchain brings to the table. Bitcoin uses blockchain technology to achieve its aims.

## Blockchain Use Cases

We are just starting to see Blockchain gain traction. There are many scenarios it potentially could be used for.

Think about any scenarios where there is a business network and assets are being traded. Removing the need for a trusted third party could greatly reduce overhead and also introduce greater transparency and validity.

So here are some interesting use cases currently being explored (beyond cryptocurrencies) :
* [Fake news detection](https://publiq.network/) - being able to validate that the news article you see, or the video you watch are the genuine thing.

* [Supply chain management](https://www.provenance.org/) - using blockchain to track products as they move through a supply chain.

* Improving [travel planning](https://travelchain.io/) - making sure all aspects of your trip are what you want and planned.

* [Healthcare](https://www.media.mit.edu/research/groups/1454/medrec) - tracking medical records, payments and many other areas.

* [Compliance and Security](https://guardtime.com/)

* [Securing voting](https://followmyvote.com/)

* [Track royalties and ownership of musical works](https://ujomusic.com/)

* [Decentralized Object storage](https://storj.io/)

### Do I Need a Blockchain

Often, blockchain is touted as the hammer for all nails. But this is more hype than anything else. Not everything will be able to, or should, use blockchain. A [study by Deloitte](https://www2.deloitte.com/insights/us/en/industry/financial-services/evolution-of-blockchain-github-platform.html) found that 92% of blockchain projects that started 2 years ago are now defunct. 

![Do I need a blockchain?]({{ site.images }}/doineedblockchain.jpg)
{:.post-image}
*Do I need a blockchain? - source: [IEEE](https://spectrum.ieee.org/computing/networks/do-you-need-a-blockchain)*
{:.image-caption}