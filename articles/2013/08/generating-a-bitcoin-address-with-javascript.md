<!--
title: Generating a Bitcoin Address with JavaScript
publish: 
slug: 2013/08/20/generating-a-bitcoin-address-with-javascript
tags: JavaScript, Bitcoin
-->

If you're not familiar with Bitcoin, Bitcoin is essentially a P2P currency that has [increased an order of magnitude in value within the last year](http://blockchain.info/charts/market-price). This [video](http://www.youtube.com/watch?v=Um63OQz3bjo) explains what it is if you're familiar with it. There are a number of libraries to work with Bitcoin any some of the most popular languages: [C](https://github.com/MatthewLM/cbitcoin), [Java](https://code.google.com/p/bitcoinj/), [C#](https://code.google.com/p/bitcoinsharp/), [Ruby](https://github.com/lian/bitcoin-ruby), [Python](https://github.com/laanwj/bitcoin-python), [Go](https://github.com/piotrnar/gocoin), and [JavaScript](https://github.com/bitcoinjs/bitcoinjs-lib). This article will focus exclusively on the JavaScript library.

**Disclaimer:** I am not a cryptographer and any such cryptography advice or implementations should be accepted as academic experimentation and not secure best practices.



Random Number Generation
------------------------

<mjax>
When $a \ne 0$, there are two solutions to \(ax^2 + bx + c = 0\) and they are
$$x = {-b \pm \sqrt{b^2-4ac} \over 2a}.$$
</mjax>

I'd be remiss if I didn't mention anything about random number generation. Random number generation is the basis of most cryptography and Bitcoin. Your Bitcoin addresses are only as secure as your random number generator. A random number generator that is said to be cryptographically secure is good enough to use for cryptography. `Math.random()` is not cryptographically secure. This is because `Math.random()` is predictable. If it's predictable, an attacked could figure out your private key from your public key.

At the time of this writing, the predominant [JavaScript Bitcoin library]() uses [CryptoJS][cryptojs] which surprisingly uses `Math.random()`. You may want to find a way to substitute with the up and coming [`window.crytpo` standard][window.crypto] or the [Stanford JavaScript Crypto Library](http://bitwiseshiftleft.github.io/sjcl/)

further reading:

- [Anatomy of a pseudorandom number generator - Visualizing Cryptocat's buggy PRNG](http://nakedsecurity.sophos.com/2013/07/09/anatomy-of-a-pseudorandom-number-generator-visualising-cryptocats-buggy-prng/)
- [What tests can I do to ensure my PRNG is working correctly?](http://crypto.stackexchange.com/questions/394/what-tests-can-i-do-to-ensure-my-prng-is-working-correctly)
- [Handbook of Applied Cryptography - Chapter 5: Pseudorandom Bits and Sequences](http://cacr.uwaterloo.ca/hac/about/chap5.pdf)
- [Bitcoin Talk: Android Key Rotation](https://bitcointalk.org/index.php?topic=271831.0)
- [Bitcoin Talk: Bad Signatures leading to 55.82 BTC theft](https://bitcointalk.org/index.php?topic=271486.0/)
- [Android Developers: Some SecureRandom Thoughts](http://android-developers.blogspot.com/2013/08/some-securerandom-thoughts.html)
- [All Android-created Bitcoin wallets vulnerable to theft](http://arstechnica.com/security/2013/08/all-android-created-bitcoin-wallets-vulnerable-to-theft/)
- [Google confirms critical Android crypto flaw used in $5,700 Bitcoin heist](http://arstechnica.com/security/2013/08/google-confirms-critical-android-crypto-flaw-used-in-5700-bitcoin-heist/)
- [Randomly failed! Weaknesses in Java Pseudo Random Number Generators](http://armoredbarista.blogspot.ch/2013/03/randomly-failed-weaknesses-in-java.html)
- [window.crypto.getRandomValues()][window.crypto]


Getting Started
---------------

You're going to want to download the [latest BitcoinJS client lib](https://raw.github.com/bitcoinjs/bitcoinjs-lib/master/build/bitcoinjs-min.js). It's pretty outdated though. There are some more recent forks: [1](https://github.com/bitfloor/bitcoinjs-lib), [2](https://github.com/twistandshout/bitcoinjs-lib), and the one that [I will eventually maintain](https://github.com/DimeJet/bitcoinjs-lib). For now, use the outdated one, from BitcoinJS.


Bitcoin Keys, Addresses, & Formats
----------------------------------

Bitcoin derives its security from the public-key crypto scheme [Elliptic Curve Cryptography (ECC)][ecc]. So why did the designer of Bitcoin, [Satoshi Nakamoto][satoshi], decide to use ECC over the prevalent RSA crypto scheme? The primary benefit is the key size. According to the [Wikipedia article on ECC](http://en.wikipedia.org/wiki/Elliptic_curve_cryptography), "a 256-bit ECC public key should provide comparable security to a 3072-bit RSA public key".

The Elliptical Curve Cryptography [spec 2.2.1][spec] states that the equations are governed by the equation:


<mjax>
  $$ y^2 = (x^3 + ax + b) \bmod p $$
</mjax>


The entire Elliptic curve domain is a sextuple, [spec 3.1.1][spec]:


<mjax>
  $$  T = (p, a, b, G, n, h) $$
</mjax>


The precise details are out of scope for this article, read the spec for more info. Bitcoin uses the [secp256k1 (info on 2.7.1)](http://www.secg.org/collateral/sec2_final.pdf) implementation, which is uses [Koblitz curves](http://en.wikipedia.org/wiki/Neal_Koblitz).

The sextuple parameters for secpk256k1 are:

- **p**: FFFFFFFF FFFFFFFF FFFFFFFF FFFFFFFF FFFFFFFF FFFFFFFF FFFFFFFE FFFFFC2F
- **a**: 0
- **b**: 7
- **G** (compressed): 02 79BE667E F9DCBBAC 55A06295 CE870B07 029BFCDB 2DCE28D9 59F2815B 16F81798
- **G** (uncompressed): 04 79BE667E F9DCBBAC 55A06295 CE870B07 029BFCDB 2DCE28D9 59F2815B 16F81798 483ADA77 26A3C465 5DA4FBFC 0E1108A8 FD17B448 A6855419 9C47D08F FB10D4B8
- **n**: FFFFFFFF FFFFFFFF FFFFFFFF FFFFFFFE BAAEDCE6 AF48A03B BFD25E8C D0364141
- **h**: 1

thus reducing the elliptic curve equation to:


<mjax>
    $$ y^2 = (x^3 + 7) \bmod p $$
</mjax>


you really don't need to understand much of this. I mainly presented this material for academic purposes.



### Private Keys

Private keys are what allows you to spend your coins. A private key, `d` is any random number between `1` and `n - 1`. According to the [spec (3.2.1)][spec]: "an elliptic
curve key pair `(d, Q)` associated with `T` consists of an elliptic curve secret key `d` which is an integer in the interval `[1, n - 1]`, and an elliptic curve public key <mjax>$ Q = (x_Q, y_Q) $</mjax> which is the point <mjax>$ Q = dG $</mjax>.

Let's generate a private key, but first we must generate our random number

```js
var tarr = new Uint8Array[32]; //create a typed array of 32 bytes (256 bits)



```



### Address

Bitcoin uses [addresses](https://en.bitcoin.it/wiki/Address) as a means to receive coins from someone else. An address is a [base58][base58] encoded string of a 25 byte binary address. All Bitcoin addresses start with `1`.  A person can have as many addresses as they'd like. Using more than one address is said to increase anonymity. 


### Public Key

The an uncompressed public key is a 65 byte long value consisting of the leading `0x04` and X,Y coordinate consisting of 32 bytes each. A compressed key  


As seen above, addresses are generated from the public ECDSA key. The public ECDSA key is generated from the private ECDSA key. Having access to the private key allows you to spend the coins associated with the address. The private key is essentially just a 256 bit random number governed by the [Secp256k1](https://en.bitcoin.it/wiki/Secp256k1) ECDSA rules.


Bitcoin addresses are generated using . Specifically, the public address is generated like the following:

> Version = 1 byte of 0 (zero); on the test network, this is 1 byte of 111
>
> Key hash = Version concatenated with RIPEMD-160(SHA-256(public key))
>
> Checksum = 1st 4 bytes of SHA-256(SHA-256(Key hash))
>
> Bitcoin Address = Base58Encode(Key hash concatenated with Checksum)
>
>
> -- <cite><a href="https://en.bitcoin.it/wiki/Protocol_specification#Addresses">Bitcoin Wiki: Addresses</a></cite> 



### Compressed Keys

An excellent [answer on the Bitcoin Stack Exchange](http://bitcoin.stackexchange.com/questions/3059/what-is-a-compressed-bitcoin-key) explains the differences:

> A compressed key is just a way of storing a public key in fewer bytes (33 instead of 65). There are no compatibility or security issues because they are precisely the same keys, just stored in a different way. The original Bitcoin software didn't use compressed keys only because their use was poorly documented in OpenSSL. They have no disadvantages other than that a little bit of additional computation is needed before they can be used to validate a signature.
> 
> If you think of a public key as a point somewhere along a giant U, an uncompressed key is the X and Y coordinates of the point. A compressed key is how high up on the U the point is along with a single bit indicating whether it's on the left or right side. As you can visualize, they both encode precisely the same thing, but the compressed form requires half as much space plus one bit. (Of course, they're really points on elliptic curve secp256k1, but the concept is the same.)
>
> -- <cite>David Schwartz</cite>

Both the address and [private key](https://en.bitcoin.it/wiki/Private_key) are encoded using the [Base 58][base58] encoding scheme.


further reading:

- [NSA: The Case for Elliptic Curve Cryptography](http://www.nsa.gov/business/programs/elliptic_curve.shtml)
- [Basic Explanation of Elliptic Curve Cryptography](http://crypto.stackexchange.com/questions/653/basic-explanation-of-elliptic-curve-cryptography)
- [Wikipedia: Elliptic Curve DSA](http://en.wikipedia.org/wiki/Elliptic_Curve_DSA)
- [Elliptic Curve Cryptography and Digital Rights Management](https://engineering.purdue.edu/kak/compsec/NewLectures/Lecture14.pdf)
- [How to create a Bitcoin Address](https://en.bitcoin.it/wiki/Technical_background_of_version_1_Bitcoin_addresses#How_to_create_Bitcoin_Address)
- [Why does Bitcoin use two hash functions (SHA-256 and RIPEMD-160) to create an address?](http://bitcoin.stackexchange.com/questions/9202/why-does-bitcoin-use-two-hash-functions-sha-256-and-ripemd-160-to-create-an-ad)
- [Can a SHA256 hash be used as an ECDSA private key?](http://bitcoin.stackexchange.com/questions/3609/can-an-sha256-hash-be-used-as-an-ecdsa-private-key)
- [What is a compressed Bitcoin key?](http://bitcoin.stackexchange.com/questions/3059/what-is-a-compressed-bitcoin-key)
- [What are Green Addresses?](http://bitcoin.stackexchange.com/questions/1730/what-are-green-addresses)
- [Compressing EC Private Keys](http://crypto.stackexchange.com/questions/1638/compressing-ec-private-keys)
- [Bitcoin Talk: secp256k1](https://bitcointalk.org/?topic=2699.0)
- [Why is it impossible to derive the public key from the address?](http://bitcoin.stackexchange.com/questions/7683/why-is-it-impossible-to-derive-public-key-from-address)
- [List of address prefixes](https://en.bitcoin.it/wiki/List_of_address_prefixes)


[bitcoinjs]: https://github.com/bitcoinjs/bitcoinjs-lib
[cryptojs]: https://code.google.com/p/crypto-js/
[window.crypto]: https://developer.mozilla.org/en-US/docs/Web/API/window.crypto.getRandomValues
[ecc]: http://en.wikipedia.org/wiki/Elliptic_curve_cryptography
[base58]: https://en.bitcoin.it/wiki/Base58Check_encoding
[wif]: https://en.bitcoin.it/wiki/Wallet_import_format
[satoshi]: https://en.bitcoin.it/wiki/Satoshi_Nakamoto
[secp256k1]:
[spec]: http://www.secg.org/collateral/sec1_final.pdf 

