<!--
title: Generating a Bitcoin Address with JavaScript
publish: 2013-08-27
slug: 2013/08/27/generating-a-bitcoin-address-with-javascript
tags: JavaScript, Bitcoin
-->

<script src="/media/2013/08/bitcoinjs-min.js"></script>

If you're not familiar with Bitcoin, Bitcoin is essentially a P2P currency that has [increased an order of magnitude in value within the last year](http://blockchain.info/charts/market-price). This [video](http://www.youtube.com/watch?v=Um63OQz3bjo) does a good job of explaining it. There are a number of libraries to work with Bitcoin in some of the most popular languages: [C](https://github.com/MatthewLM/cbitcoin), [Java](https://code.google.com/p/bitcoinj/), [C#](https://code.google.com/p/bitcoinsharp/), [Ruby](https://github.com/lian/bitcoin-ruby), [Python](https://github.com/laanwj/bitcoin-python), [Go](https://github.com/piotrnar/gocoin), and [JavaScript](https://github.com/bitcoinjs/bitcoinjs-lib). This article will focus exclusively on the JavaScript library.

**Disclaimer:** I am not a cryptographer and any such cryptography advice or implementations should be accepted as academic experimentation and not crypto best practices.



Random Number Generation
------------------------

I'd be remiss if I didn't mention anything about random number generation. Random number generation is the basis of most cryptography and Bitcoin. Your Bitcoin addresses are only as secure as your random number generator. A random number generator that is said to be cryptographically secure is good enough to use for cryptography. `Math.random()` is not cryptographically secure. This is because `Math.random()` is predictable. If it's predictable, an attacker could figure out your private key from your public key. The implications of someone else knowing your private key means that they can also spend your Bitcoins.

At the time of this writing, the predominant [JavaScript Bitcoin library]() uses [CryptoJS][cryptojs] which surprisingly uses `Math.random()`. This article shows how you can use the up and coming [`window.crytpo` standard][window.crypto] or the [Stanford JavaScript Crypto Library](http://bitwiseshiftleft.github.io/sjcl/)

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

You're going to want to use the [latest BitcoinJS client lib](https://raw.github.com/bitcoinjs/bitcoinjs-lib/master/build/bitcoinjs-min.js). It's pretty outdated though. There are some more recent forks: [1](https://github.com/bitfloor/bitcoinjs-lib), [2](https://github.com/twistandshout/bitcoinjs-lib), and the one that [I will eventually maintain](https://github.com/DimeJet/bitcoinjs-lib). For now, use the outdated one, from BitcoinJS.

I have included the library on this page. Just open up your Chrome Console or Firefox Console and start typing along.


Bitcoin Keys, Addresses, & Formats
----------------------------------

Bitcoin derives its security from the public-key crypto scheme [Elliptic Curve Cryptography (ECC)][ecc]. So why did the designer of Bitcoin, [Satoshi Nakamoto][satoshi], decide to use ECC over the prevalent RSA crypto scheme? The primary benefit is the key size. According to the [Wikipedia article on ECC](http://en.wikipedia.org/wiki/Elliptic_curve_cryptography), "a 256-bit ECC public key should provide comparable security to a 3072-bit RSA public key".

The Elliptical Curve Cryptography [spec 2.2.1][spec] states that the cryptography is governed by the equation:


<mjax>
  $$ y^2 = (x^3 + ax + b) \bmod p $$
</mjax>


The entire Elliptic curve domain is a sextuple, [spec 3.1.1][spec]:


<mjax>
  $$  T = (p, a, b, G, n, h) $$
</mjax>


The precise details are out of scope for this article, read the spec for more info. Bitcoin uses the [secp256k1 (info on 2.7.1)](http://www.secg.org/collateral/sec2_final.pdf) implementation, which uses [Koblitz curves](http://en.wikipedia.org/wiki/Neal_Koblitz).

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

You'll notice that we generate 32 random values. And `n` does not have a maximum of <mjax>$ 2^{256} - 1 $</mjax>, so it's theoretically possible to generate a key larger than the standard dictates. However, in practice, you really don't have to worry about it.

let's generate a private key:

```js
var randArr = new Uint8Array[32] //create a typed array of 32 bytes (256 bits)
window.crypto.getRandomValues(randArr) //populate array with cryptographically secure random numbers

//some Bitcoin and Crypto methods don't like Uint8Array for input. They expect regular JS arrays.
var privateKeyBytes = []
for (var i = 0; i < randArr.length; ++i)
  privateKeyBytes[i] = randArr[i]

//hex string of our private key
var privateKeyHex = Crypto.util.bytesToHex(privateKeyBytes).toUpperCase()
console.log(privateKeyHex) //1184CD2CDD640CA42CFC3A091C51D549B2F016D454B2774019C2B2D2E08529FD
```

simple enough, huh? But wait, this doesn't look like the private keys that you see in your Bitcoin clients. So, what's going on? Private keys typically use a format called the [Wallet Import Format (WIF)](https://en.bitcoin.it/wiki/Wallet_import_format).


#### Wallet Import Format

The Wallet Import Format (WIF) is shorter way to encode the private key. It is a [base 58][base58] encoding of `0x80` + private key + checksum.

generate a WIF in JS:

```js
//add 0x80 to the front, https://en.bitcoin.it/wiki/List_of_address_prefixes
var privateKeyAndVersion = "80" + privateKeyHex
var firstSHA = Crypto.SHA256(Crypto.util.hexToBytes(privateKeyAndVersion))
var secondSHA = Crypto.SHA256(Crypto.util.hexToBytes(firstSHA))
var checksum = secondSHA.substr(0, 8).toUpperCase()
console.log(checksum) //"206EC97E"

//append checksum to end of the private key and version
var keyWithChecksum = privateKeyAndVersion + checksum
console.log(keyWithChecksum) //"801184CD2CDD640CA42CFC3A091C51D549B2F016D454B2774019C2B2D2E08529FD206EC97E"

var privateKeyWIF = Bitcoin.Base58.encode(Crypto.util.hexToBytes(keyWithChecksum))
console.log(privateKeyWIF) //"5Hx15HFGyep2CfPxsJKe2fXJsCVn5DEiyoeGGF6JZjGbTRnqfiD"
```

You can verify that this checks out by looking at either at http://brainwallet.org and entering the value of `privateKeyHex` for the input to **Secret Exponent**.  You can also verify http://gobittest.appspot.com/PrivateKey.

However, there is a much easier method to use:

```js
//recall, "privateKeyBytes" is an array of random numbers
var privateKeyWIF = new Bitcoin.Address(privateKeyBytes) 
privateKeyWIF.version = 0x80 //0x80 = 128, https://en.bitcoin.it/wiki/List_of_address_prefixes
privateKeyWIF = privateKeyWIF.toString()
console.log(privateKeyWIF) //"5Hx15HFGyep2CfPxsJKe2fXJsCVn5DEiyoeGGF6JZjGbTRnqfiD"
```



### Public Keys

Now that we have our private key, we can generate a public key. Recall that Bitcoin keys use the [secp256k1 (info on 2.7.1)](http://www.secg.org/collateral/sec2_final.pdf) parameters. Public keys are generated by:

<mjax>

$$ Q = dG $$

</mjax>

where <mjax>$ Q $</mjax> is the public key, <mjax>$ d $</mjax> is the private key, and <mjax>$ G $</mjax> is a curve parameter. A public key is a 65 byte long value consisting of a leading `0x04` and X and Y coordinates of 32 bytes each.


```js
var curve = getSECCurveByName("secp256k1") //found in bitcoinjs-lib/src/jsbn/sec.js

//convert our random array or private key to a Big Integer
var privateKeyBN = BigInteger.fromByteArrayUnsigned(input) 

var curvePt = curve.getG().multiply(privateKeyBN)
var x = curvePt.getX().toBigInteger()
var y = curvePt.getY().toBigInteger()
var publicKeyBytes = integerToBytes(x,32) //integerToBytes is found in bitcoinjs-lib/src/ecdsa.js
publicKeyBytes = publicKeyBytes.concat(integerToBytes(y,32))
publicKeyBytes.unshift(0x04)
var publicKeyHex = Crypto.util.bytesToHex(publicKeyBytes)

console.log(publicKeyHex)
/* output:
04d0988bfa799f7d7ef9ab3de97ef481cd0f75d2367ad456607647edde665d6f6
fbdd594388756a7beaf73b4822bc22d36e9bda7db82df2b8b623673eefc0b7495
*/
```

this output can be verified with http://brainwallet.org.


#### Compressed Keys

You may have heard of Compressed keys. An excellent [answer on the Bitcoin Stack Exchange](http://bitcoin.stackexchange.com/questions/3059/what-is-a-compressed-bitcoin-key) explains the differences:

> A compressed key is just a way of storing a public key in fewer bytes (33 instead of 65). There are no compatibility or security issues because they are precisely the same keys, just stored in a different way. The original Bitcoin software didn't use compressed keys only because their use was poorly documented in OpenSSL. They have no disadvantages other than that a little bit of additional computation is needed before they can be used to validate a signature.
> 
> If you think of a public key as a point somewhere along a giant U, an uncompressed key is the X and Y coordinates of the point. A compressed key is how high up on the U the point is along with a single bit indicating whether it's on the left or right side. As you can visualize, they both encode precisely the same thing, but the compressed form requires half as much space plus one bit. (Of course, they're really points on elliptic curve secp256k1, but the concept is the same.)
>
> -- <cite>David Schwartz</cite>

There is little reason to use uncompressed keys. Let's generate a compressed public key:

```js
var publicKeyBytesCompressed = integerToBytes(x,32) //x from above
if (y.isEven())
  publicKeyBytesCompressed.unshift(0x02)
else
  publicKeyBytesCompressed.unshift(0x03)

var publicKeyHexCompressed = Crypto.util.bytesToHex(publicKeyBytesCompressed)
console.log(publicKeyHexCompressed)
/* output:
03d0988bfa799f7d7ef9ab3de97ef481cd0f75d2367ad456607647edde665d6f6f
*/
```

You can see that this matches up to http://brainwallet.org when **Compressed** is clicked.

It's possible to do all of this in many shorter steps:

```js
//privateKeyBytes is the private key array from the top
var eckey = new Bitcoin.ECKey(privateKeyBytes) 
var publicKeyHex = Crypto.util.bytesToHex(eckey.getPub())
console.log(publicKeyHex)
/* output:
04d0988bfa799f7d7ef9ab3de97ef481cd0f75d2367ad456607647edde665d6f6
fbdd594388756a7beaf73b4822bc22d36e9bda7db82df2b8b623673eefc0b7495
*/

eckey.compressed = true
var publicKeyHexCompressed = Crypto.util.bytesToHex(eckey.getPub())
```

#### Compressed Private Keys

You may have noticed that when you toggle back and forth between **Compressed** and **Uncompressed** on http://brainwallet.org that the Private Key changes as well. So, if you import a private key into your wallet, which public key will it use? Another [good answer on Bitcoin Stack Exchange](http://bitcoin.stackexchange.com/questions/7299/when-importing-private-keys-will-compressed-or-uncompressed-format-be-used) on how to deal with this:

> ...
> Thus, in order to support both, we must remember for each public/private keypair whether the normal or compressed encoding is to be used. As you point out, we also need this information when importing a private key. To do so, the "Wallet Import Format" for private keys (the base58 form, typically starting with a '5'), was extended. If the public key/address for a particular private key are to be derived from the compressed encoding of the public key, the private key gets an extra 0x01 byte at the end, resulting in a base58 form that starts with 'K' or 'L'.
>
> So to answer your question: when importing a private key into the reference client, it will use the normal encoding for public keys if the '5' format was used for the private key, and the compressed encoding if the 'K'/'L' format was used. It doesn't make sense to try to convert one to the other: the client must use the same encoding as was used when generating the address, or the address won't match. Unfortunately, quite a lot of software doesn't support compressed public keys yet (which is a pity, as they save block chain space).
>
> -- <cite>Pieter Wuille</cite>

let's generate our private compressed key:

```js
var privateKeyBytesCompressed = privateKeyBytes.slice(0) //clone array
privateKeyBytesCompressed.push(0x01)
var privateKeyWIFCompressed = new Bitcoin.Address(privateKeyBytesCompressed)
privateKeyWIFCompressed.version = 0x80
privateKeyWIFCompressed = privateKeyWIFCompressed.toString()

console.log(privateKeyWIFCompressed) //KwomKti1X3tYJUUMb1TGSM2mrZk1wb1aHisUNHCQXTZq5auC2qc3
```

Once again, verify it matches http://brainwallet.org.



### Addresses

Bitcoin uses [addresses](https://en.bitcoin.it/wiki/Address) as a means to receive coins from someone else. An address is a [base58][base58] encoded string of a 25 byte binary address. All Bitcoin addresses start with `1`.  A person can have as many addresses as they'd like. Using more than one address is said to increase anonymity. Private keys give you access to spend money associated with an address.

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

Compressed, uncompressed, zomg... yes, there are **two addresses** associated with each ECC private key. The procedure is exactly the same:

```js
//could use publicKeyBytesCompressed as well
var hash160 = Crypto.RIPEMD160(Crypto.util.hexToBytes(Crypto.SHA256(publicKeyBytes)))
console.log(hash160) //"3c176e659bea0f29a3e9bf7880c112b1b31b4dc8"

var version = 0x00 //if using testnet, would use 0x6F or 111.
var hashAndBytes = Crypto.util.hexToBytes(hash160)
hashAndBytes.unshift(version)

var doubleSHA = Crypto.SHA256(Crypto.util.hexToBytes(Crypto.SHA256(hashAndBytes)))
var addressChecksum = doubleSHA.substr(0,8)
console.log(addressChecksum) //26268187

var unencodedAddress = "00" + hash160 + addressChecksum

console.log(unencodedAddress)
/* output
  003c176e659bea0f29a3e9bf7880c112b1b31b4dc826268187
*/

var address = Bitcoin.Base58.encode(Crypto.util.hexToBytes(unencodedAddress))
console.log(address) //16UjcYNBG9GTK4uq2f7yYEbuifqCzoLMGS
```

even easier...

```js
var address = new Bitcoin.Address(Crypto.util.hexToBytes(hash160))
address.version = 0x00 //testnet would be 0x6F
address = address.toString()
```

ok, the easiest of all...

```js
var address = eckey.getBicoinAddress().toString() //"eckey" from above
console.log(address) ////16UjcYNBG9GTK4uq2f7yYEbuifqCzoLMGS

//you must generate a new one if you already called getBitcoinAddress() for the
//address representing the uncompressed version
var eckey2 = new Bitcoin.ECKey(privateKeyBytes)
eckey2.compressed = true
var addressForCompressed = eckey2.getBitcoinAddress().toString()
console.log(addressForCompressed) //1FkKMsKNJqWSDvTvETqcCeHcUQQ64kSC6s
```

### Double Hashing

OK, you've probably noticed it a lot. Once again, I'm going to defer to the internets and the peeps smarter than me:

From: [Why does Bitcoin use two hash functions (SHA-256 and RIPEMD-160) to create an address?](http://bitcoin.stackexchange.com/questions/9202/why-does-bitcoin-use-two-hash-functions-sha-256-and-ripemd-160-to-create-an-ad)

> RIPEMD was used because it produces the shortest hashes whose uniqueness is still sufficiently assured. This allows Bitcoin addresses to be shorter.
>
> SHA256 is used as well because Bitcoin's use of a hash of a public key might create unique weaknesses due to unexpected interactions between RIPEMD and ECDSA (the public key signature algorithm). Interposing an additional and very different hash operation between RIPEMD and ECDSA makes it almost inconceivable that there might be a way to find address collisions that is significantly easier than brute force trying a large number of secret keys.
>
> Essentially, it was a belt and suspenders approach. Bitcoin had to do something unique and rather than have to hope they got it exactly right, they overdesigned it.
>
> -- <cite>David Schwartz</cite>

and on the SHA256(SHA256(input)):

> So why does he hash twice? I suspect it's in order to prevent length-extension attacks.
>
> SHA-2, like all Merkle-Damgard hashes suffers from a property called "length-extension". This allows an attacker who knows H(x) to calculate H(x||y) without knowing x. This is usually not a problem, but there are some uses where it totally breaks the security. The most relevant example is using H(k||m) as MAC, where an attacker can easily calculate a MAC for m||m'. I don't think Bitcoin ever uses hashes in a way that would suffer from length extensions, but I guess Satoshi went with the safe choice of preventing it everywhere.
> 
> To avoid this property, Ferguson and Schneier suggested using SHA256d = SHA256(SHA256(x)) which avoids length-extension attacks. This construction has some minor weaknesses (not relevant to bitcoin), so I wouldn't recommend it for new protocols, and would use HMAC with constant key, or truncated SHA512 instead.
> 
> Answered by CodesInChaos


## Summary

Compressed keys are the preferred format now. Let's do this in one quick JS snippet to make everything stupidly simple and compatible with pretty much every Bitcoin client:

```js
var randArr = new Uint8Array[32] //create a typed array of 32 bytes (256 bits)
window.crypto.getRandomValues(randArr) //populate array with cryptographically secure random numbers

//some Bitcoin and Crypto methods don't like Uint8Array for input. They expect regular JS arrays.
var privateKeyBytes = []
for (var i = 0; i < randArr.length; ++i)
  privateKeyBytes[i] = randArr[i]

var eckey = new Bitcoin.ECKey(privateKeyBytes)
eckey.compressed = true
var address = eckey.getBitcoinAddress().toString()
console.log(address)// 1FkKMsKNJqWSDvTvETqcCeHcUQQ64kSC6s


var privateKeyBytesCompressed = privateKeyBytes.slice(0) //clone array
privateKeyBytesCompressed.push(0x01)
var privateKeyWIFCompressed = new Bitcoin.Address(privateKeyBytesCompressed)
privateKeyWIFCompressed.version = 0x80
privateKeyWIFCompressed = privateKeyWIFCompressed.toString()

console.log(privateKeyWIFCompressed) //KwomKti1X3tYJUUMb1TGSM2mrZk1wb1aHisUNHCQXTZq5auC2qc3
```

There you have it! **KwomKti1X3tYJUUMb1TGSM2mrZk1wb1aHisUNHCQXTZq5auC2qc3** is your private key in WIF form that allows you to send money for address **1FkKMsKNJqWSDvTvETqcCeHcUQQ64kSC6s**.


#### Addresses and Keys from a Password or Passphrase

You can generate passwords and passwords from a passphrase. One method would be to just calculate the double SHA256 hash on the user passphrase and use that as the private key.

```js
var password = "there can be only one"
var privateKeyHex = Crytpo.util.SHA256(Crypto.util.hexToBytes(Crypto.util.SHA256(password)))
var privateKeyBytes = Crypto.util.hexToBytes(privateKeyHex)
```


further reading:

- [NSA: The Case for Elliptic Curve Cryptography](http://www.nsa.gov/business/programs/elliptic_curve.shtml)
- [Basic Explanation of Elliptic Curve Cryptography](http://crypto.stackexchange.com/questions/653/basic-explanation-of-elliptic-curve-cryptography)
- [Wikipedia: Elliptic Curve DSA](http://en.wikipedia.org/wiki/Elliptic_Curve_DSA)
- [Elliptic Curve Cryptography and Digital Rights Management](https://engineering.purdue.edu/kak/compsec/NewLectures/Lecture14.pdf)
- [How to create a Bitcoin Address](https://en.bitcoin.it/wiki/Technical_background_of_version_1_Bitcoin_addresses#How_to_create_Bitcoin_Address)
- [Can a SHA256 hash be used as an ECDSA private key?](http://bitcoin.stackexchange.com/questions/3609/can-an-sha256-hash-be-used-as-an-ecdsa-private-key)
- [What are Green Addresses?](http://bitcoin.stackexchange.com/questions/1730/what-are-green-addresses)
- [Compressing EC Private Keys](http://crypto.stackexchange.com/questions/1638/compressing-ec-private-keys)
- [Bitcoin Talk: secp256k1](https://bitcointalk.org/?topic=2699.0)
- [Why is it impossible to derive the public key from the address?](http://bitcoin.stackexchange.com/questions/7683/why-is-it-impossible-to-derive-public-key-from-address)
- [Why are Bitcoin Addresses Hashes of Public Keys?](http://bitcoin.stackexchange.com/questions/3600/why-are-bitcoin-addresses-hashes-of-public-keys)
- [List of address prefixes](https://en.bitcoin.it/wiki/List_of_address_prefixes)

**Credits:** Combing through the http://brainwallet.org was a big help.


[bitcoinjs]: https://github.com/bitcoinjs/bitcoinjs-lib
[cryptojs]: https://code.google.com/p/crypto-js/
[window.crypto]: https://developer.mozilla.org/en-US/docs/Web/API/window.crypto.getRandomValues
[ecc]: http://en.wikipedia.org/wiki/Elliptic_curve_cryptography
[base58]: https://en.bitcoin.it/wiki/Base58Check_encoding
[wif]: https://en.bitcoin.it/wiki/Wallet_import_format
[satoshi]: https://en.bitcoin.it/wiki/Satoshi_Nakamoto
[spec]: http://www.secg.org/collateral/sec1_final.pdf 

