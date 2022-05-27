---
title: Making games with NFT’s and Web3.js
date: 2021-10-21
---

## NTF’s in games

By now I’m sure you’re aware of what Non-fungible Tokens (NFT’s) are. A multi-billion dollar industry has risen in what seems overnight, with digital assets ranging from artwork, trading cards, music and title deeds.

The gaming industry has been on a steady rise ([23% last year](https://www.techtimes.com/articles/265703/20210922/how-gamified-nfts-are-changing-the-future-of-gaming.htm#:~:text=Per%20one%20report%20in%202020%2C%20the%20industry%20saw%20a%20growth%20of%2023.1%25.)), and play-to-earn games using NFT’s are booming. Axie Infinity is one example which has [2.5 billion dollars in trading volume](https://markets.businessinsider.com/news/currencies/top-nft-collection-q3-axie-infinity-crypto-gaming-expands-2021-10) since its inception.

So, how can you get involved?

## Where to create NFT’s?

OpenSea is the most popular marketplace for NFT’s, they allow minting on the Ethereum network, and if you want to avoid the high gas fees they support the Polygon network which you can use for free.

Most games will use their own platform for NFT’s, using smart contracts released on the Ethereum network with tools like [remix](https://remix-project.org/) and [truffle](https://trufflesuite.com/). This allows them to automatically mint NFT’s such as when a player breeds a new creature.

However if you are just starting out with this technology, using [OpenSea’s API](https://docs.opensea.io/reference/getting-assets) can be a beginner-friendly entry into using NFT’s with your games.

By manually minting some NFT’s, you can use their API for simple HTTP requests to see if a particular user has an asset (at this time the API doesn’t support assets on the Polygon network).

The user is identified by their wallet address, which you can communicate with using the library [web3.js](https://web3js.readthedocs.io/en/v1.5.2/), the most popular wallet being [MetaMask](https://metamask.io/).

![Melon Mash Game](/assets/images/2021/melonSplash.jpeg)

## Melon Mash

This is the path I took for [Melon Mash](https://apps.mbmedia.co.nz/melon-mash/), a simple puzzle game I released on the [Apple App Store](https://apps.apple.com/ma/app/melon-mash-fruity-fun/id1545427969) and the [Google Play Store](https://play.google.com/store/apps/details?id=com.ravengames.melonmash). A tetris-like game, the idea is to clear lines of fruit and unlock new fruit types.

I wanted to add NFT’s into the game to allow the player to unlock special types of fruit not available otherwise. Here is the [gold watermelon NFT](https://opensea.io/assets/0x495f947276749ce646f68ac8c248420045cb7b5e/30804129840082260625448751376425370773788606332098241421188088393508933600232) I created for this, and how it shows up in-game:

![Melon Mash Game](/assets/images/2021/melonNFT.jpeg)

When the user connects their wallet, the game recognises they have the Gold Watermelon NFT.

![Melon Mash Game](/assets/images/2021/melonUnlocked.jpeg)

I used the popular software [Gamemaker Studio](https://www.yoyogames.com/en) to make my game and I can export it to HTML5 to run in a browser.

You can see an example of my HTML game [here](https://apps.mbmedia.co.nz/melon-mash/game).

## Web3.js and Gamemaker Studio

To integrate Web3, I had to create an extension in GameMaker, add a custom JS file where my code will go, and the web3.min.js file to communicate with the users wallet.

We just need two functions in the custom js file. The first is to see if the user is using a browser that has a wallet connected to it.

```javascript
function checkMetaConnection() {
    if (typeof window.ethereum !== 'undefined') {
        return 1;
    } else {
        return 0;
    }
}
```

We can now call this function in GameMaker with checkMetaConnection().

The second function will get the user’s wallet address.

```javascript
// Ask user to connect wallet to site and get address
async function getMetamaskAccount() {
    var map = {};
    map["id"] = "getWalletAddress";
    map["address"]="0";
    try {
        const accounts = await ethereum.request({ method: 'eth_requestAccounts' });
        map["address"] = accounts[0];
        web3 = new Web3(window.ethereum);
    } catch(error) {
        console.log("User rejected request");
    }
    GMS_API.send_async_event_social(map);
}
```

This uses a special Gamemaker callback with a ds_map. We can listen for this call in GameMaker through the Async-Social event:

```javascript
var my_id = async_load[? "id"];
    if (my_id=="getWalletAddress") {
        global.user_address = async_load[? "address"];
    if (global.user_address=="0") {
        show_debug_message("Denied access to wallet");
    } else {
        show_debug_message("Got user address as"+string(global.user_address));
    }
}
```

Now to check if the user has a certain NFT.

A GameMaker forum user [JesterOC](https://forum.yoyogames.com/index.php?members/jesteroc.1541/) has created a [demo example on Github](https://github.com/JesterOC3/JesterOC_OpenSea.io-REST-API-for-GMS).

Basically it’s just an HTTP call:

`https://api.opensea.io/api/v1/asset/CONTRACT_ADDRESS/NFT_TOKEN_ADDRESS?account_address=WALLET_ADDRESS`

Which will return a json map and include whether the supplied wallet address owns the NFT token.

You can download his example on Github, and take it further with [my Github example of connecting to Metamask](https://github.com/mytchallb/gamemaker-web3) which includes a function to check for token balances.

## Conclusion

These examples work for HTML5 games in browsers that support web3.js.

NFT’s open a wide variety of possibilities within the gaming industry, and with the range of tools and frameworks available, anyone can create a NFT-compatible game and join in on this booming industry, even using beginner-friendly tools like GameMaker Studio.

_Note: This article was first published on [Medium](https://medium.com/@mytchall.b/using-games-with-nfts-and-web3-js-610fd5fbd562)_