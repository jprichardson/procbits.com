<!--
title: Generating a Bitcoin Address with JavaScript
publish: 2013-08-17
tags: JavaScript, Bitcoin
-->


Random Number Generation
------------------------

I'd be remiss if I didn't mention anything about random number generation. Random number generation is the basis of most cryptography and Bitcoin. Your Bitcoin addresses are only as secure as your random number generator. A random number generator that is said to be cryptographically secure is good enough to use for cryptography. `Math.random()` is not cryptographically secure. This is because `Math.random()` is predictable. If it's predictable, an attacked could figure out your private key from your public key.

further reading:

- [Anatomy of a pseudorandom number generator - Visualizing Cryptocat's buggy PRNG](http://nakedsecurity.sophos.com/2013/07/09/anatomy-of-a-pseudorandom-number-generator-visualising-cryptocats-buggy-prng/)
- [What tests can I do to ensure my PRNG is working correctly?](http://crypto.stackexchange.com/questions/394/what-tests-can-i-do-to-ensure-my-prng-is-working-correctly)
- [Handbook of Applied Cryptography - Chapter 5: Pseudorandom Bits and Sequences](http://cacr.uwaterloo.ca/hac/about/chap5.pdf)
- [Bitcoin Talk: Android Key Rotation](https://bitcointalk.org/index.php?topic=271831.0)
- [Bitcoin Talk: Bad Signatures leading to 55.82 BTC theft](https://bitcointalk.org/index.php?topic=271486.0/)
- [Android Developers: Some SecureRandom Thoughts](http://android-developers.blogspot.com/2013/08/some-securerandom-thoughts.html)
- [All Android-created Bitcoin wallets vulnerable to theft](http://arstechnica.com/security/2013/08/all-android-created-bitcoin-wallets-vulnerable-to-theft/)
- [Google confirms critical Android crypto flaw used in $5,700 Bitcoin heist](http://arstechnica.com/security/2013/08/google-confirms-critical-android-crypto-flaw-used-in-5700-bitcoin-heist/)
- [window.crypto.getRandomValues()](https://developer.mozilla.org/en-US/docs/Web/API/window.crypto.getRandomValues)


