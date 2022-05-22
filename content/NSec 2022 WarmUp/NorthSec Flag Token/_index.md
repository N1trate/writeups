+++
title = "NorthSec Flag Token"
description = ""
weight = 5
+++

Challenge statement:
```
Jon is lost in the metaverse, and wants to get his hands on a sweet sweet NFT of this collection (alt link). Can you hack the blockchain to grab one for yourself?

RULES
DO NOT ATTACK WEBSITES, API OR USERS; DO NOT TRY TO BUY NFTs; ONLY USE PLAIN RINKEBY TESTNET BLOCKCHAIN INTERACTIONS!

Send me an Ethereum personal signature of "NFT stands for NorthSec Flag Token" (without quotes) from the wallet owning the NFT. Do not leak your signature as it can be used by anyone to solve the challenge. You can use https://app.mycrypto.com/sign-message or any other tools to create the signature which is a string that starts with 0x.
```

The collection can be seen [here](https://testnets.opensea.io/collection/nsec-warmup-2022) or [here](https://rinkeby.looksrare.org/collections/0x994f85881DB567b2d07ABb46Ad7814fA49EcAc77)

The first step is to have access to an Eth wallet. The easiest way is to install MetaMask in your browser.

The second step is to get a bit of Eth to be able to generate transactions. We need to get some on faucets like https://faucets.chain.link/rinkeby.

By browsing the collection, we can see that a few token were owned by 0xc745a32262b944ABd01a119598BBB6e7654779a3 and 0x836A3cBEB8a962707a9387Db5C80bA9508a04Dc5. I had at first not seen, but the last NFT was owned by 0x0b055ded5fc8c8b107fcFD8f22B76E1C5D59490F.

We can scour the list of transactions done by that first address on [etherscan](https://rinkeby.etherscan.io/address/0xc745a32262b944abd01a119598bbb6e7654779a3). Most of the challenge was doable by using the etherscan website and looking at the Transactions and Contract tabs.

This [transaction](https://rinkeby.etherscan.io/tx/0xb2e30fe81a2e72e9945b2bc3bfdfce2cb3d3fbdcb81bafb7c7437bd7d169cf21) is the creation of the contract for the challenge's NFT marketplace.

We can then go check the contract located at [this address](https://rinkeby.etherscan.io/address/0x994f85881db567b2d07abb46ad7814fa49ecac77) which will probably have a lot of failed transactions by the time that you read this writeup. That address could also be found directly on the collection page, which would have been lightly faster.

We can see [the](https://rinkeby.etherscan.io/tx/0xebfd506bf625ba6d59e6d246b5a327e2a779528c52880a6086799561f4cc273c) [first](https://rinkeby.etherscan.io/tx/0xa9d1bafafe95a554a6e052f354da8deaa51a6d0328dd807e22a246ca31cd3642) [three](https://rinkeby.etherscan.io/tx/0x14df0ca9067052133f62cde12ed8151230e291edc4faa5583a3c586832079595) NFT being minted directly on the marketplace, but that turned out to be a dead end.

Looking at the marketplace contract, we could also see two interesting transactions, one for [Approve Admin](https://rinkeby.etherscan.io/tx/0x8f93621aac1d196b816779bc2ab86689bff41efec5db24803d46e30f3f5618e7) and one for [Revoke Admin](https://rinkeby.etherscan.io/tx/0x85d9d9d2213227a7940d754dfa05617082f601085d5de3d8c16c18fca4aa7e35). Those two specify the address [0x836a3cbeb8a962707a9387db5c80ba9508a04dc5](https://rinkeby.etherscan.io/address/0x836a3cbeb8a962707a9387db5c80ba9508a04dc5). Looking at that address' transactions, we can see the creation of two Giveaway contracts, with the first one's giveaway function called twice, which created the NFT [4](https://rinkeby.etherscan.io/tx/0x9887b2d39fc48d4230330e67b2161803e9b2102b844fcbde5271c47fed0e71f3) and [5](https://rinkeby.etherscan.io/tx/0x15befb5eacfe5113dd7b4d8086358f6599e5c8411c917bf7491e72f38ddb3d57).

Going on the [Givaway contract](https://rinkeby.etherscan.io/address/0x59c6dccc5abcab256747101ef0d4c4fe8b2b8367) which created those NFT, we can call the giveaway function the same way and generate a new NFT that would be given to our address. The source address needed to be the marketplace, and the target our own address.

On the Contract tab, we can see the actual code behind the giveaway function:
```javascript
/**
 * @title GiveAway NSEC2022 WARMUP
 * @dev Give away NFTs!
 */
contract GiveAway is CreatorExtensionBasic {
 
    constructor() {}
    
    /**
     * @dev This public function is used to give away NFT!
     * @param source The source contract address to mint the NFT from
     * @param target The target wallet address to receive the minted NFT
     */
    function giveaway(address source, address target) public {
        IERC721CreatorCore(source).mintExtension(target);
    }
}
```
The same function can be found at the bottom of the [second Giveaway contract](https://rinkeby.etherscan.io/address/0x62162e569f076d80173acda7cf53b1703cf7f32c#code).

From the marketplace, it was also possible to find the last original NFT owner's address in this [transaction](https://rinkeby.etherscan.io/tx/0xbc4f3c621d8e6ea74fd96601cde3516499437c7a8b4d8e9ed2aefb2361d90ffe) which interacts with the first Giveaway contract to invoke the [giveaway](https://rinkeby.etherscan.io/tx/0xc8d444000f5a109352d179dbca65e8050dfeadeafe4b6121157527ecc75229f8) function the same way, to create the sixth NFT.

If we keep looking at the transactions made by the marketplace creator, we can find one calling [registerExtension](https://rinkeby.etherscan.io/tx/0x8ebfbe7504468e19c5db3ce0600658f04af86dc7d3575f57dff485219824679b) that is specifying the address of that first Giveaway contract. The second parameter, the baseURI, doesn't related to https://studio.api.manifoldxyz.dev/asset_uploader/asset/2852698318/metadata/full which contains metadata for the tokens.

After receiving the NFT, we had to sign a message and send it to the flag validator. The signature was done using [MyCrypto](https://app.mycrypto.com/sign-message), which generated a sig in the proper format.