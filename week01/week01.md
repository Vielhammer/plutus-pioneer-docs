# Plutus Pioneer Program
## Lecture 1

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
<img src="https://github.com/Vielhammer/plutus-pioneer-docs/blob/master/week01/images/image-20210918195543160.png" alt="image-20210918195543160" style="width:200px; height: 300" />

In the example above, Alice has 100 ada, and Bob has 50 ada. Alice wants to send 10 of her ada to Bob.  Each transaction will always have inputs and outputs. However, only completed UTxO's can be inputs. For example, Alice cannot split her 100 ada into 90 and 10. She has to use the full UTxO of 100 ada as the input. 


