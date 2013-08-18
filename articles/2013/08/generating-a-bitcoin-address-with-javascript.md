<!--
title: Generating a Bitcoin Address with JavaScript
publish: 2013-08-17
tags: JavaScript, Bitcoin
-->


Random Number Generation
------------------------

I'd be remiss if I didn't mention anything about random number generation. Random number generation is the basis of most cryptography and Bitcoin. Your Bitcoin addresses are only as secure as your random number generator. A random number generator that is said to be cryptographically secure is good enough to use for cryptography. `Math.random()` is not cryptographically secure. This is because `Math.random()` is predictable. If it's predictable, an attacked could figure out your private key from your public key.

At the time of this writing, the predominant [JavaScript Bitcoin library]() uses [CryptoJS][cryptojs] which surprisingly uses `Math.random()`. You may want to find a way to substitute with the up and coming [`window.crytpo` standard][window.crypto].

further reading:

- [Anatomy of a pseudorandom number generator - Visualizing Cryptocat's buggy PRNG](http://nakedsecurity.sophos.com/2013/07/09/anatomy-of-a-pseudorandom-number-generator-visualising-cryptocats-buggy-prng/)
- [What tests can I do to ensure my PRNG is working correctly?](http://crypto.stackexchange.com/questions/394/what-tests-can-i-do-to-ensure-my-prng-is-working-correctly)
- [Handbook of Applied Cryptography - Chapter 5: Pseudorandom Bits and Sequences](http://cacr.uwaterloo.ca/hac/about/chap5.pdf)
- [Bitcoin Talk: Android Key Rotation](https://bitcointalk.org/index.php?topic=271831.0)
- [Bitcoin Talk: Bad Signatures leading to 55.82 BTC theft](https://bitcointalk.org/index.php?topic=271486.0/)
- [Android Developers: Some SecureRandom Thoughts](http://android-developers.blogspot.com/2013/08/some-securerandom-thoughts.html)
- [All Android-created Bitcoin wallets vulnerable to theft](http://arstechnica.com/security/2013/08/all-android-created-bitcoin-wallets-vulnerable-to-theft/)
- [Google confirms critical Android crypto flaw used in $5,700 Bitcoin heist](http://arstechnica.com/security/2013/08/google-confirms-critical-android-crypto-flaw-used-in-5700-bitcoin-heist/)
- [window.crypto.getRandomValues()][window.crypto]




Bitcoin Keys, Addresses, & Formats
----------------------------------

Bitcoin addresses are generated using the public-key crypto scheme [Elliptic Curve Cryptography][ecc]. Specifically, the public address is [generated](https://en.bitcoin.it/wiki/Protocol_specification#Addresses) like the following:

> Version = 1 byte of 0 (zero); on the test network, this is 1 byte of 111
> Key hash = Version concatenated with RIPEMD-160(SHA-256(public key))
> Checksum = 1st 4 bytes of SHA-256(SHA-256(Key hash))
> Bitcoin Address = Base58Encode(Key hash concatenated with Checksum)
>
> -- <cite>Bitcoin Wiki: Addresses</cite> 



[bitcoinjs]: https://github.com/bitcoinjs/bitcoinjs-lib
[cryptojs]: https://code.google.com/p/crypto-js/
[window.crypto]: https://developer.mozilla.org/en-US/docs/Web/API/window.crypto.getRandomValues
[ecc]: http://en.wikipedia.org/wiki/Elliptic_curve_cryptography