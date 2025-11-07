**checksum algorithm**
--- 
# Elliptic Curve Cryptography Overview
#elliptic-curve-cryptography
- link: https://www.youtube.com/watch?v=dCvB-mhkT0w
- has been around mid 1980s, but not implemented since recently or used extensively
- is a [public key crypto] system crytography
- [trapdoor] function: 
    - In theoretical computer science and cryptography, a trapdoor function is a function that is easy to compute in one direction, yet difficult to compute in the opposite direction (finding its inverse) without special information, called the "trapdoor". Trapdoor functions are a special case of one-way functions and are widely used in public-key cryptography.        
    - is a math function that kind of underpins the public key crypto ststems
    - the basic around it is: you can take a given value A and use the trapdoor function and going one way to get to value B. You can't get back to A from B.
---
# Elliptic Curve Cryptography versus. RSA
- RSA is based on [prime number factorization] where you take two random prime numbers u multiply them together u get this really big prime number --> the issue is that it is easy to achieve 
- key size aspect: ECC (256 bit) vs. RSA (3072 bit) | ECC (384 bit) vs. RSA(7680 bit) - same level of security 
- 384 bit is T.S. (top secret) - top secret level information in the US government. NSA and other government agencies have said that elliptic curve 384B keys are strong enough to protect it
- it is symetric about the x-axis
- if u draw a straight line through this curve, it will intersect the curve in no more than three points. If we were going to draw a line through point A, it will hit another couple of points (B, C).
- [DOT function] --> A.DOT(something)
- we can DOT a number of time. 
- The max value we can DOT == key size
- If we increase the key size --> increase the value to be used
---
# Trapdoor vs Hashing
#trapdoor #hashing

---
#cryptography 
- [ ] Implement PKCS#7 padding
	  https://cryptopals.com/sets/2/challenges/9
- [ ] Mode of operations
      https://cryptopp.com/wiki/Modes_of_Operation 
- [ ] Initialization Vector (IV)
      https://cryptopp.com/wiki/Initialization_Vector
- [ ] Implement CBC mode
	https://cryptopals.com/sets/2/challenges/10
- [ ] CBC encrypt/decrypt process
	- [ ] why need PKCS7 ?
	- [ ] what is IV ?
- [ ] GCM vs CBC
	- [ ] abbreviation ?
		- [ ] GCM: 
		- [ ] CBC: 
	https://cryptopp.com/wiki/Initialization_Vector
- [ ] Difference botgateway & dvc decryption
	- [ ] bot gateway: 
- [ ] why need to encode base64 ?
