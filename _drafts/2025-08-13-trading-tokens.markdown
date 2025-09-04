---
layout: post
title:  "Ramping up on off-ramping in 2025"
categories: web3
---

In many Web3 companies, part of your paycheck comes in tokens instead of dollars.
It sounds futuristic —- and it is —- but unless your landlord has started accepting crypto,
you still need to convert those funny-shaped tokens into fiat to cover everyday expenses like rent.
So, how does this token-to-cash process actually work in 2025?
Let's look at the process and shed some light at the underlying machinery.

The scenario is that you work for some web3 company that has its own token.
Maybe it even has its own chain.
The first decision you face is where to convert:
decentralized (DEX) or centralized exchange (CEX).
At a high level, a DEX is implemented as a program running on the blockchain,
while a CEX is just a regular web2 application.
While DEXes are great for swapping between tokens, you’ll ultimately need a CEX to bridge into fiat.
As far as I'm aware, a decentralized exchange won't help with that.

So, off you go and create an account with a CEX that's legally allowed to operate in your jurisdiction.
Only after you went through the usual know-your-customer (KYC) process and started exploring do you discover that you cannot exchange your tokens living on some random L2 straight into cash.
Luckily, the exchange did integrate with the particular L2 in question,
so you at least don't need to bridge/move the tokens somewhere else first,
but only to swap to/from ETH.
So, as a first step, I needed to swap my $TOKENs into more mainstream Ethereum.

To keep things interesting, aligned with the web3 spirit and for educational purposes,
I decided to use a decentralized exchange.
Actually, since the CEX I signed up with did not support my particular token anyway,
I kinda had no choice but to go with a DEX.
I was not in the mood to sign up and/or research other CEXes at this point.
As an alternative, the popular in-browser wallet extension MetaMask apparently also supports swapping straight through their UI conveniently abstracting all the nitty-gritty (for a fee, of course).

The next question is which DEX to use, since there are many.
On ZKsync, for example, [SyncSwap](https://syncswap.xyz) and [PancakeSwap](https://pancakeswap.finance/swap) are among the [more active ones](https://zksync.dappradar.com/rankings/defi?category=defi_dex&page=1&sort=totalVolumeInFiat&order=desc).
Going into all the details of how the various DEXes differ is certainly intriguing,
but it needs a separate exposition to do the topic justice.
Some of the more prominent dimensions include volume (some have more activity than others), token pairs (some have more pairs you can trade than others) and fees (some may be higher, some may be lower).

I ultimately went with SyncSwap as it seemed to give me better exchange rates than the alternatives.
Plus, it had more activity, which is also a positive signal.
I ended up swapping most of my $TOKENs into USDC and a small fraction into Eth.
My reasoning was that USDC shields from the high volatility, although you also miss out on any potential future appreciations of your token (but also protects against depreciation, it's all about framing/perspective).
As I only later discovered: if you swap $TOKEN for USDC, the conversion involves intermediate steps.
What happened in my case is that we went from $TOKEN to Eth to USDC.e to USDC.
I'm assuming this is because there is such a plethora of tokens,
[direct swaps between arbitrary pairs](https://syncswap.xyz/pools) do not always exist.
In such a case, the trade needs potentially multiple intermediate steps via some mainstream token.
Unfortunately, multi-hop trades have higher fees (gas and trading), but such is life.

<!-- ![Image]({{ site.baseurl }}/images/2025-08-13-trading-tokens-route.png "title"){: width="70%" } -->

<img src="/images/2025-08-13-trading-tokens-route.png" alt="Image" title="title" style="display:block;float:none;margin-left:auto;margin-right:auto;width:80%"/>

So, now we have $TOKEN, USDC and Eth living on our L2.
The next step is to actually sell some of the Eth for USD (no C!).
This works by sending Eth to an address the CEX generates for you.
If I transfer funds into this address, they will show up in my CEX web2 account.
Importantly, don't get confused about what to transfer where here.
If you screw this up, the tokens can most likely not be recovered.
No support hotline you can reach.
Social consensus won't help either, because it doesn't care if you lose a couple grand due to carelessness/stupidity.
So, double-check the network and sending/receiving addresses.
Naturally, doing it for the first time, this part made me a bit anxious -- transferring funds to some random address the CEX spits out.
I did the prudent thing here, and only transferred a tiny amount (0.02 Eth) to test the flow,
all the while crossing my fingers hoping the tokens won't disappear into thin air (or, rather, ether).
Luckily, I received an email about two hours later notifying me of the successful deposit.
Confirmed that the flow works as intended, I transferred the remaining Eth I intended to cash out.

From then on, it's rather straightforward. Once the funds show up in your CEX account,
you use the UI to swap them into fiat (for a fee, of course) and send the fiat to a regular bank account.
For example, in my case, being in the EU and using SEPA for the final leg, the funds showed up more or less immediately in my bank account.
Also, if you are in the EU, there's an additional confirmation required if the transferred amount is over 1K EUR.
You need to click a dialog somewhere in the CEX UI acknowledging that you own/control the funds origin address.

There was one more moment I briefly thought I effed up somewhere.
Apparently, when moving ERC20 tokens between accounts,
the "to" address is not the address of the receiving account,
but rather the ERC20 token address, i.e., the transfer is implemented by a smart contract function instead of "natively".
I first moved my funds to a "staging" address, the idea being that if I screw up or my wallet software is infected,
I only lose the tokens sitting in the staging address.
So when I looked at the transfer transaction in the block explorer,
I was briefly shocked and confused to see a different "to" address then the one I intended to send the funds to.

This is, in 2025, how you go from fancy new internet money to being able to pay your utility bills in conventional old-school money. As always, do-your-own-research (DYOR) as far as DEXes, CEXes, fees, etc. go.
