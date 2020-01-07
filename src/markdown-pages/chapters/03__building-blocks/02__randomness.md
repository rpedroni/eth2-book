---
path: "/chapters/building-blocks/randomness"
title: "Randomness: RANDAO"
---

We just explored the basic structure of the Beacon Chain. We briefly mentioned the fact that the Beacon Chain assigns validators to each slot. So how does the Beacon Chain actually do that?

It all comes down to **randomness**.

## Wait, What *is* Randomness?
Our lives seem to be punctuated by an endless stream of random events. Whether we're thinking about packing an umbrella for that trip next week, or just sitting down for a game of D&D, we're taking small gambles every day.

Though not without its faults, randomness bestows upon us many of our greatest joys. Without it, life would be entirely predictable and, perhaps, unbearably mundane. In many ways, we're truly lucky when we're able to say that tomorrow holds unknown possibilities.

But what exactly *is* randomness? We might have an intuitive feel for the meaning of the word "random," but most of us would probably have a difficult time explaining the concept in detail. So, let's explore the idea of randomness in a little more depth.

### Randomness is Qualitative
A dictionary might describe randomness as something that has a quality of unpredictability, fitting for the highly chaotic experiences of human life. It seems, though, that certain things are simply "more random" than others. Weather patterns, for example, can seem very random, but we're able to predict them to at least *some* extent. Lottery outcomes are pretty random, but ask anyone to pick a random number between one and ten, and they're probably going pick seven.

When we talk about an event being "random," or something presenting "randomness," therefore, we're actually making a *qualitative* statement. So, when we say that something is "more" random than something else, we're talking about exactly *how* unpredictable a given event might be. The weather is, clearly, much more predictable than the lottery. Or my sleep schedule.

Furthermore, we might be using randomness, like our computers do, as a way to defend ourselves against others. In these cases, we're also interested in understanding the ability for someone to "bias," or manipulate, our randomness. If someone can very successfully manipulate our source of randomness, then we might *think* that it's random when it's really being carefully controlled. The ability of someone to bias our randomness too, however, is a spectrum.

### Randomness in Computing
Computers are usually meant to execute programs in expected, or deterministic, ways. We typically want our programs to generate the same output when given the same input. Our lives would be much more difficult if our computers started behaving in completely "random" ways. 

Our computers do still, clearly, rely on random numbers for a lot of important functionality. For instance, computers need access to random numbers in order to generate Ethereum private keys. Otherwise, anyone else could easily reproduce the behavior of our computer and, therefore, reproduce our private keys. Clearly there must be some way for computers to generate "randomness." 

Computers generally rely on the idea of "reasonably unpredictable" external inputs in order to generate random numbers. For instance, a program might derive random numbers from the movement of your mouse over a period of time. Mouse movements are generally very unpredictable, so it's unlikely that any two people will move their mice across their screens in exactly the same way. Cursors can take so many potential paths across a screen that it's also infeasible for an algorithm to "guess" which movements you used to generate a given random number.

Unpredictable inputs can take many different forms. [Random.org](https://random.org) generates random numbers from the chaotic radio noise generated by natural processes in our atmosphere. CloudFlare, an internet infrastructure company, famously derives randomness from a camera pointed at a wall of lava lamps. We have no shortage of potentially "random" inputs for our computers.

### Randomness in Blockchains
Random inputs are effective for traditional computers because we trust them to behave in some expected way. CloudFlare's lava lamps wouldn't be very effective if the live-stream were frozen. Mouse movements wouldn't be useful if someone could hack our mouse to lie to our computer about where the cursor has been.

Although we usually don't worry about these things in our personal computers, we definitely need to consider them in the context of a blockchain. Let's explore how this might work in the context of Ethereum.

Ethereum, unlike the computers we're familiar with, is isolated to the outside world. Data can only enter Ethereum via **transactions**. In order to insert a random number into Ethereum, therefore, someone would have to be responsible for creating the transaction that holds this random number. However, if we assign this job to a single individual, then we have no way to know whether they're *really* publishing random numbers or just publishing numbers that *look* random. Maybe they're publishing the specific "random" number that just happens to win them a game of Ethereum-based poker.

People have been tackling this problem for a long time. Solutions are varied, but they generally tend to center around the idea of minimizing the ability for any *one* individual to manipulate the random number. Usually, solutions achieve this by having many different individuals collectively create a random number.

## Enter: RANDAO
RANDAO is a mechanism for generating "reasonably random" numbers in a decentralized way. The core idea behind RANDAO is to have a large group of people come together to generate a random number, instead of simply trusting one person to do it on everyone's behalf. 

The simplest version of RANDAO is to have each member of a group come up with a random number on their own. We can then take these random numbers and "mix" them together in a way that each person's number has an equal impact on the final result.

![Open RANDAO Mix](./images/randomness/randao-mix-open.png)

Unfortunately, there's a flaw here. Someone could simply wait to see all of the other numbers before picking their own! Since our "mixing" algorithm needs to be known in advance (we wouldn't be able to verify that the numbers were actually mixed correctly otherwise), the last person to pick can freely test out numbers until they find one they like.

So how do we fix this? We use an old schoolyard trick: get everyone to write down their numbers before putting them together.

Since we're in the world of computers, we're not actually using physical pieces of paper. Instead, we have everyone create a **cryptographic commitment** for their random number. Wait, a cryptographic what?

Okay, let's say you're playing a game of "guess the number" with a friend who likes to cheat. Your friend (secretly) picks the number "five," and you correctly guess it. Since this isn't a great friend, the (secret) number was suddenly "six" the whole time. You're suspicious, but you have no evidence.

You don't like losing to cheaters, so you have an idea. The next time you play, you make your friend whisper their (secret) number to another, mutually trusted, friend. No cheating this time, Marc.

Cryptographic commitments are, basically, the mathematical equivalent of whispering your random number to that second friend. Except without that necessary step of having friends in the first place. Cryptographic commitments "hide" a secret in a way that someone can't lie about the original secret later on. You can't figure out the secret from the commitment alone, but if you can *recreate* the commitment using the secret you're given, then that secret must've been the original.

Now our "commit-reveal" RANDAO process looks more like this:

![Closed RANDAO Mix](./images/randomness/randao-mix-closed.png)

Of course, as is always the case, there's one more flaw here. Cryptographic commitments stop someone from changing their number at the last second, but they don't actually force someone to reveal their number in the first place.

This flaw doesn't break RANDAO entirely, but it does make it somewhat subject to bias. The last person to reveal only gets one more attempt to find a favorable number. If the last `n` people to reveal are in cahoots, then they'll get a total of `2^n` attempts. Again, much better than the unlimited attempts in our version of RANDAO without commitments, but not perfect.

Generally speaking, though, "commit-reveal" RANDAO is "good enough" for the purposes of Eth2. The Beacon Chain runs RANDAO once every epoch. Within each block, the assigned validator reveals a commitment. At the end of every epoch, these revealed values are mixed together to produce a random number.

### An Aside: Verifiable Delay Functions
It's possible to improve upon the "commit-reveal" version of RANDAO using some more fancy math. Although not currently included in the official Eth2 specification, Verifiable Delay Functions, or VDFs, were developed by researchers at Stanford in 2018 to this end. VDFs are, effectively, algorithms that take a long time to execute and can't be sped up by running the algorithm on multiple computers at the same time.

VDFs take some `input` value and return an `output` along with a `proof` of correctness for that `output`. Although it takes a long time to compute the `output`, it only takes a short period of time to use the `proof` to verify that the `output` was, indeed, correct. We can tune a parameter in the VDF to change the amount of time that an average computer will take to find an `output`.

VDFs are interesting in their own right, but they shine in combination with RANDAO. Instead of simply using the result of RANDAO as our random number, we can first feed this result into a VDF and use the `output` as our final random number.

![RANDAO With VDFS](./images/randomness/randao-with-vdfs.png)

So how does this help? Well, the attackers in our last example only really gain an advantage because they can easily check whether it's worth revealing their number or not. If we first limit the amount of time that people have to reveal their random numbers and then tune the VDF to take A Very Long Time™, then any would-be attacker shouldn't be able to see the effect of their reveal in time.

Of course, this is all fundamentally based on the idea the attackers don't have access to computer hardware that can compute VDFs very quickly. If an attacker somehow *is* able to determine the effect of their reveal before time is up, then we're back to good 'ol "commit-reveal" RANDAO. A VDF research group was recently coordinated with the goal of producing low-cost hardware that approaches the limits for VDF computation time. If this project is successful, then we may see VDFs officially introduced to Eth2 in the near future.

## Randomness in Eth2
We've talked a lot about randomness, and we now know how the Beacon Chain actally generate random numbers with the help of RANDAO. So what are these random numbers actually used for? Let's find out.

### Block Proposers
Blockchains don't work unless someone is around to produce blocks. Although there are many ways to select block producers in a Proof of Stake system, we generally want to maintain a few key properties.

First, we want the selection process to be fair to all of the validators. Blockchains generally give out a reward to block producers, so we want to ensure that validators are being paid in proportion to their investment in the system. A good block proposal mechanism should therefore make sure that validators are given the opportunity to produce blocks at a rate mostly proportional to their stake in the system. For example, a validator with 10% of the total stake in the system should produce approximately 10% of all blocks. Simple enough, in theory.

If, in our blockchain, each validator has the same amount of stake, then we could just sort the validators into a list (alphabetically, perhaps) and allow validators to produce a block based on their position in this list. We'd get a perfect distribution of blocks between validators every time, no randomness required. We could even extend this system to handle the case that validators have different amounts of stake.

This is, basically, a "round-robin" system. It's easy to implement, but it's also quite vulnerable to certain types of attacks. Particularly, round-robin selection tells us far in advance who's going to be producing blocks and when. Any would-be adversary would have plenty of time to strategically attack specific validators in order to manipulate certain blocks. An adversary might try block a validator from the rest of the network in order to prevent that validator from producing a block in a Denial-of-Service attack. Validators could even try to collude with their neighbors on the list to stop producing blocks and, as a result, halt the network for a significant period of time.

If we use random numbers to pick new block producers for every epoch, then these attacks become much more difficult to execute. As long as the random number generation process is difficult to manipulate or predict, it'll be significantly harder to deny service to or collude with other validators. The law of large numbers also guarantees that the distribution of blocks will, over time, generally reflect the amount of stake owned by each validator.

### Committees
Eth2 relies on the "committees," defined groups of validators, to perform certain actions like vote on the validity of blocks within shards. Committees are particularly important to the security of Eth2's sharding system. If an attacker can gain control of 2/3rds of a committee voting on the validity of a shard block, then they can cause an invalid shard block, perhaps one that generates ETH out of thin air, to be considered valid by the rest of the network.

Although Eth2 does allow users to submit "fraud proofs" in these events, the Beacon Chain would still be forced to roll itself back to a point before the invalid shard block. This roll-back would be a major service disruption, so we need a good source of randomness that reduces the probability of this sort of event.

### Applications
Many blockchain applications also need good sources of randomness. For example, the various lottery applications already running on Eth1 are only as good as their mechanism for generating random numbers. So far, applications have typically either relied on relatively poor sources of randomness, like block hashes, or attempted to roll their own versions of the random number generation process used in Eth2. We don't want application developers to have to build complex protocols for primitives like randomness, so it's important that Eth2 provides a secure and accessible source of randomness.

## A Quick RuNDAOn
RANDAO, VDFs, cryptographic commitments? Randomness can be a lot sometimes! Luckily, now we're familiar with the process of selecting block proposers. So how do we verify that given block actually came from a specific block proposer? Let's find out.