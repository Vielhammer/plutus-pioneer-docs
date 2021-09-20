# Plutus Pioneer Program: [Lecture 1](https://youtu.be/_zr3W8cgzIQ)

Plutus is a functional development platform for distributed contract applications on the Cardano blockchain. It provides considerable security advantages by validating the contract off-chain before it moves on-chain. Plutus offers a new approach with both the on and off-chain code written in the same language.

Plutus programming is not easy. This course will be challenging for several reasons. First, Plutus uses the Extended Unspent Transaction Output (EUTxO) model. It is different and less intuitive than how Ethereum handles smart contracts.

Plutus is brand new and under rapid development. Throughout the course, there will probably be significant changes. You may write contracts at the beginning of the course that compile, which may not at the end. 

The tooling is not ideal yet. If you are familiar with Haskell and its tooling, you may find that it may not be as pleasant in the presence of Plutus. You may find it easier to use one or more of the following: Nix, Cabal, or Docker. The team is working on a Docker image that will be released later.

Plutus is embedded in Haskell and will be familiar if you are knowledgeable of Haskell. You will need to have a firm grasp of Haskell to understand Plutus. There is a [Haskell course](https://www.youtube.com/playlist?list=PLJ3w5xyG4JWmBVIigNBytJhvSSfZZzfTm) that IOHK hosted in Mongolia in 2020 that is available for free. The content in the videos posted is specific to concepts required for writing Plutus.

Another problem is that Plutus is brand new. The IOHK team and Plutus Pioneers are the first people on the planet to write Plutus code. There is no Stack Overflow or searching Google for questions. 

The [Plutus Community Documentation](https://docs.plutus-community.com/) is an excellent resource for getting up and running. The [Discord server](https://discord.gg/) is a resource for asking questions and helping each other.

<br>

## The UTxO Model

One of the most important concepts to understand before beginning Plutus programming is understanding the accounting model of Cardano. 

Cardano uses the Extended Unspent Transaction Output (EUTxO) model. Before we get to the EUTxO model, it is vital to understand the UTxO model.

Unlike traditional banking that flows from account to account, the UTxO model flows from transaction to transaction. Each transaction has inputs (where the funds come from) and outputs (where the funds are going). Instead of having an account with a ledger that carries a remaining balance, we have a UTxO. Simply put, UTxO's are transaction outputs from previous transactions that occurred on the blockchain but are unspent.



>  *Bitcoin uses the UTxO model.*

<br>

### UTxO Example with Two Inputs
<img src="https://github.com/Vielhammer/plutus-pioneer-docs/blob/master/week01/images/image-20210918195543160.png" alt="image-20210918195543160" style="width:300px; height: 400" />

In the example above, Alice has 100 ada, and Bob has 50 ada. Alice wants to send 10 of her ada to Bob.  Each transaction will always have inputs and outputs. However, only completed UTxO's can be inputs. For example, Alice cannot split her 100 ada into 90 and 10. She has to use the full UTxO of 100 ada as the input. 

<img src="https://github.com/Vielhammer/plutus-pioneer-docs/blob/master/week01/images/image-20210918195609508.png" alt="image-20210918195609508" style="width:300px; height: 400" />

It doesn't work like traditional banking models. For example, if Alice had $100 in her account, she could go to an ATM or send a wire or ACH to Bob for $10. On the banking ledger, $10 would deduct from Alice's account.

<img src="https://github.com/Vielhammer/plutus-pioneer-docs/blob/master/week01/images/image-20210918195738613.png" alt="image-20210918195738613" style="width:300px; height: 400" />

Alice creates the transaction for 100 ada. Now it is no longer a UTxO. It was spent. One output will give Bob 10 ada, and the other will return her change of 90 ada. This transaction consumes the whole UTxO (input) that Alice created and output two new UTxO's (90 and 10, respectively).

In any transaction, the sum of the inputs must equal the output value. If 100 ada is input, 100 ada must be the output. 

> There are two exceptions to this rule:
>
> - transactions fees
> - native tokens

<br>

### UTxO Example with Three Inputs
<img src="https://github.com/Vielhammer/plutus-pioneer-docs/blob/master/week01/images/image-20210918195817247.png" alt="image-20210918195817247" style="width:300px; height: 400" />

In this example, both Alice and Bob want to send Charlie 55 ada each. The transaction has three inputs, Alice's 90 ada, Bob's 50, and Bob has 10 ada.

<img src="https://github.com/Vielhammer/plutus-pioneer-docs/blob/master/week01/images/image-20210918195836830.png" alt="image-20210918195836830" style="width:300px; height: 400" />

Bob has to use both of his UTxO's to send 55 ada because neither UTxO by itself equals 55.

<img src="https://github.com/Vielhammer/plutus-pioneer-docs/blob/master/week01/images/image-20210918195946547.png" alt="image-20210918195946547" style="width:300px; height: 400" />

Charlie then receives 110 ada from Alice and Bob. 

A new layer to introduce is that a transaction can only be consumed when a signature is attached. If a signature was not required, Bob could send Alice's ada without her permission. In the [first example](#utxo-example-with-two-inputs), Alice is creating the transaction, so only her signature would be required.

In this example, Alice and Bob are sending ada. Therefore, both signatures would be required to complete the transaction.

Everything covered so far is about the UTxO model. In the examples, we sent ada from one or more addresses (Alice and Bob) to another address (Charlie).

<br>

## The EUTxO Model
<img src="https://github.com/Vielhammer/plutus-pioneer-docs/blob/master/week01/images/image-20210918200032243.png" alt="image-20210918200032243" style="width:300px; height: 400" />

In this example, we will focus on one transaction that consumes a single UTxO input. The validator that decides whether the transaction is allowed to consume the UTxO relies on digital signatures. Alice would have to sign that transaction for it to be valid.

In the EUTxO model, we replace the signature with arbitrary logic. That is where Plutus comes in. Instead of having an address that belongs to a public key verified by a signature added to the transaction. We have more general addresses that are not based on public keys or the hashes of public keys. They contain arbitrary logic that can decide under which conditions a transaction can spend a UTxO.

In lieu of an address going to a public key, like Alice's key, there will be a script that contains arbitrary logic instead of the signature. The input would confirm if it can consume the output with some data, called the redeemer.

<img src="https://github.com/Vielhammer/plutus-pioneer-docs/blob/master/week01/images/image-20210918200053892.png" alt="image-20210918200053892" style="width:300px; height: 400" />

In the EUTxO model, we replace the public key address (Alice) with a script. A redeemer (data) takes the place of the digital signature. The next question is, what is arbitrary logic in the redeemer?

It is essential to consider what information and context the script has. 

In the diagram above, the only thing the script can see is the redeemer. The only information that the script has to decide whether it is permissible for the transaction to consume this UTxO or not is looking at the redeemer.

Above is the most basic concept, and Bitcoin does this with Bitcoin script. There is a script on the UTxO side and a redeemer on the input side. The script gets the redeemer and can use the redeemer to decide whether or not to consume the UTxO or not.

<img src="https://github.com/Vielhammer/plutus-pioneer-docs/blob/master/week01/images/image-20210918200106154.png" alt="image-20210918200106154" style="width:300px; height: 400" />

Ethereum uses a different concept where the script can see everything - the whole state of the blockchain. In Bitcoin, it's the extreme opposite. The script can only view the redeemer.

Ethereum's Solidity can see the entire state of the blockchain. Allowing this enables Ethereum's scripts to be much more powerful. They can do anything, but that comes with problems. Because the script is so powerful, it is difficult to predict what the outcome of the script running will be. This opens the door to security issues and dangers due to the unpredictability of the script.

What Cardano does is something in the middle. It doesn't offer such a restricted view as Bitcoin but not such a global view as Ethereum. The script does not see the whole blockchain but can see the entire transaction that it is processing for validation. Plutus scripts can then use that information to determine whether it can consume the output.

### EUTxO Example
In this example, there is only one input. If there were additional inputs, the script would be able to see those as well. There is one final ingredient that Plutus scripts need to be as powerful and impressive as Solidity, and that is datum. Datum is a piece of data that can be associated with the UTxO in addition to the value.

<img src="https://github.com/Vielhammer/plutus-pioneer-docs/blob/master/week01/images/image-20210918200138112.png" alt="image-20210918200138112" style="width:300px; height: 400" />

At the script address, in addition to the 100 ada value, an arbitrary piece of data is attached. It is called a datum.

With this, we can mathematically prove that Plutus is at least as powerful as Solidity. Any script that runs on Solidity can run here. There are a lot of advantages over the Ethereum model. In Plutus, it is possible to check whether a transaction will validate in the wallet before being sent to the chain.

Something can still go wrong. The transaction can consume an UTxO, and when it gets to the chain, another transaction may have already consumed it. This situation is unavoidable. 

If that occurs, the transaction will fail without having to pay any fees. If all the inputs are available, the transaction validates and the script will have the desired effect. That is not the case with Solidity.

In addition, it is easier to analyze a Plutus script. Checking, verifying, and proving that it is secure is straightforward because you are not considering the whole blockchain, which is unknowable. You can concentrate on just the context of a single transaction. Because the scope is much more limited it's much easier to understand what the script is doing and predict what it will do.

One thing not mentioned yet is who is responsible for providing the datum, script, and the redeemer. The rule is that the spending transaction has to provide the information. The producing transaction will have to deliver the hashes.

If Alice produces an output, she must include the script's hash and the datum's hash that belongs to the output. Optionally, it can provide the datum and the script. If a transaction wants to consume a script output, it must include the script, datum, and the redeemer.

To spend a given input, you need to know the datum. Only the hash is publicly visible on the blockchain. That may not be desirable in every case, so the option to include the datum and script is available to the producer.


### Smart Contracts

Let's examine an actual smart contract. The concept of this contract is an auction. Someone has something to sell, like an NFT (non-fungible token). The auction is parameterized by the owner, with a minimum bid and deadline.

<img src="https://github.com/Vielhammer/plutus-pioneer-docs/blob/master/week01/images/image-20210918200210244.png" alt="image-20210918200210244" style="width:300px; height: 400" />

Alice wants to auction an NFT. She creates a UTxO at the script output. The script, in this case, is the auction script. The value of the UTxO is just the NFT. The datum doesn't exist at this stage.

>  An important note, on the real blockchain, a UTxO cannot just be native tokens. There has to be ada attached, but for this example, we will ignore that requirement.

<img src="https://github.com/Vielhammer/plutus-pioneer-docs/blob/master/week01/images/image-20210918200221721.png" alt="image-20210918200221721" style="width:300px; height: 400" />

Let's say Bob wants to bid 100 ada. The transaction to achieve this will require two inputs. The first input is the auction UTxO that Alice created, and the second is Bob's bid. The output is at the auction script, now the value has changed, and the datum is attached.

Now there is a bid, and the datum changes to Bob's address and the bid amount. The value has changed because it is no longer just the NFT. It now contains the 100 ada bid that Bob made. As a redeemer to unlock the original auction UTxO, we use a function named bid.

The auction script will check if all the conditions are satisfied. The script has to check if the bid meets the following requirements: 

- Does it meet the deadline? 
- Does the bid satisfy the minimum bid amount
- Did it provide the correct inputs and outputs

If all the conditions are satisfied, the transaction completes.

<img src="https://github.com/Vielhammer/plutus-pioneer-docs/blob/master/week01/images/image-20210918200302715.png" alt="image-20210918200302715" style="width:300px; height: 400" />

Next, Charlie wants to make a bid of 200 ada. Charlie creates a new transaction. This time, we have two inputs and two outputs. The inputs are Charlie's bid and the auction UTxO. The outputs are the updated auction UTxO, with Charlie's bid and the original UTxO. The second output is Bob's bid of 100 ada, which returned to him.

The bid redeemer has the responsibility to validate the parameters. If the deadline parameter is outstanding and the new bid is higher than the last bid, the conditions are satisfied. The transaction completes, and Bob gets his bid back.

<img src="https://github.com/Vielhammer/plutus-pioneer-docs/blob/master/week01/images/image-20210918200329667.png" alt="image-20210918200329667" style="width:300px; height: 400" />

Let's assume for our example that Charlie's bid is the last. Once the deadline is satisfied, the auction can be closed. To do that, someone has to create another transaction. That could be Alice who wants the ada for her NFT or Charlie, who wants the NFT. Both parties have an incentive to close the auction.

This transaction will have one input, the auction UTxO, and two outputs. One output is Charlie, the NFT, and Alice gets the highest bid. The redeemer checks if the deadline parameter is valid, the highest bidder received the NFT, and the owner received the highest bid.



### No Bid Example
<img src="https://github.com/Vielhammer/plutus-pioneer-docs/blob/master/week01/images/image-20210918200339588.png" alt="image-20210918200339588" style="width:300px; height: 400" />

Alice starts the auction, but no bids are received. In this case, there must be a mechanism where Alice can retrieve her NFT. Since there are no bidders, the NFT doesn't go to the highest bidder. The NFT will return to Alice. She would need to trigger this transaction since all of the parameters (expiration date, minimum bid) were not satisfied.