# Introduction to Block Ciphers
Now we are moving on to modern ciphers. Block ciphers are probably the most used encryption algorithms in the world.

## Encoding
Historical ciphers mostly operate on letters since they are pen and paper methods. When working with digital data, we can represent letters, numbers, symbols, etc as strings of bits (bytes). Thus, the communicating parties must first agree on an encoding (e.g. ASCII, UTF8, etc). We still see examples where encoding fails, but for the rest of the course, let's assume the communicating parties have agreed on an encoding.

ASCII can represent 2^8 characters, which is 256. The table starts from 0 and goes up to 255. The first part of the original section. There are about 96 characters that can be printed on-screen.

![Pasted image 20211105204521.png](Pasted%20image%2020211105204521.png)

## Cryptography Goals
The main topic we talk about is confidentiality. Cryptography solves this problem by securing a channel. This can be done by block ciphers, stream ciphers, authenticated encryption, or public-key encryption.

Data authentication can be done using hash functions, MAC algorithms, and authenticated encryption. Then there are digital signatures for origin non-repudiation, entity authentication, and zero-knowledge proofs.

![Pasted image 20211105204610.png](Pasted%20image%2020211105204610.png)

## Cryptographic Algorithms
We can divide cryptographic algorithms into two categories:
1. Keyless: cryptographic hash functions.
2. Keyed algorithms: secret/symmetric key and public/asymmetric key cryptography.

### Secret Key Algorithms
Secret key algorithms use the same key material for both encryption and decryption, hence the name symmetric key cryptography. There are three types of algorithms in this category:
1. Block ciphers
2. Stream ciphers
3. Message Authentication Codes (MACs)

Block ciphers and stream ciphers are encryption primitives, whereas message authentication codes are used for data and data origin authentication. However, we can use a block cipher to build both a stream cipher and a message authentication code, even a keyless hash function.

### Asymmetric Cryptosystems
Different types of algorithms in asymmetric key cryptography include key agreement algorithms, public-key encryption algorithms, and digital signature algorithms.

## Symmetric Cryptosystems
The ciphertext should appear random to prevent someone from obtaining information about the plaintext. The symmetric keys used for encryption and decryption are identical or closely related (i.e. one can be obtained from the other in polynomial time). With asymmetric encryption, the public key is used for encryption, and the private key is used for decryption.

![Pasted image 20211105205521.png](Pasted%20image%2020211105205521.png)

## Block Ciphers
Block ciphers operate on *b*-bit blocks of data. Plaintext is divided into *b*-bit blocks. Each block is encrypted by a secret key *k* to produce *b*-bit output. Output blocks form the ciphertext. Regardless of the size of the plaintext, the output will be a fixed length (*b*-bits). A block cipher and chosen key is actually a permutation (the act of arranging the members of a set into a sequence or order) from 2^*b* elements to 2^*b* elements.

Most block ciphers use *b* = 64 or *b* = 128 (128 is better). *k* is normally 128-, 192-, or 256-bits.

### Permutations
2^*b* factorial is approximately 2^(*b*-1)^2^*b* (a very huge number) different permutations on *b*-bit input blocks. When you design a block cipher and choose a key, you will be selecting a small subset of this huge space. A good block cipher means the keys you select are good permutations.

![Pasted image 20211105210314.png](Pasted%20image%2020211105210314.png)

A block cipher will provide only a tiny fraction of all these available permutations for typical values of *b* and *k*, namely 2^*k*.

For security, for any chosen key, we expect a good block cipher to act like it's a randomly selected permutation. Furthermore, we expect no relation between permutations that are obtained by keys that are related somehow. We will return to this when talking about related-key cryptanalysis.

## Block Cipher Design Principles
This goes back to Claude Shannon (1916-2001), who is considered 'the father of information theory'. He contributed to the field of cryptanalysis for US defence during WW2. His landmark paper 'Communication Theory of Secrecy Systems' (1949) introduced the twin ideas of confusion and diffusion for practical cipher design. His definitions are:

- Confusion: 'to make the relation between the simple statistics of the ciphertext and the simple description of the key a very complex and involved one'.
- Diffusion: 'the statistical structure of the plaintext which leads to its redundancy is dissipated into long range statistics in the cryptogram'.

Note that these concepts are not measurable, absolute concepts. These definitions are somewhat abstract.

Massey simplified these definitions:

![Pasted image 20211105210739.png](Pasted%20image%2020211105210739.png)

## Block Cipher Design
Block ciphers can mostly be categorised into two groups:
- Substitution Permutation Network (SPN)
- Feistel Network

A cipher mostly consists of the repetition of a round *r* times. Instead of using the key directly in every round, a key schedule algorithm is used that generates round keys from the secret key.

### Substitution Permutation Network (SPN)
A round of an SPN consists of 3 layers:
1. Key addition: this combines the key material with the plaintext.
2. Substitution: this provides confusion.
3. Permutation: this provides diffusion.

AES and PRESENT are examples of an SPN.

The key schedule produces round keys. If you don't have an add round key at the end, then they can go upwards and perform the inverse of the permutation and substitution. Since the attacker doesn't know the round key, they can't capture intermediate values. With the add round key at the beginning, they can't go forward if they know the plaintext since they don't know the round key. We're assuming all the details of the algorithm are public, and the only secret information is the key, which gets used to produce the round keys.

![Pasted image 20211107165407.png](Pasted%20image%2020211107165407.png)

#### PRESENT cipher
PRESENT is an ISO/IEC Standard for lightweight crypto. The block size is 64-bits, the key length is 80- or 128-bits, and there are 31 rounds. The key schedule algorithm is similar between the different key sizes. An 80-bit key can be bruteforced.

This diagram represents one round. The Ki represents 64-bits of the key. The S represents the S-box, which is the confusion layer. Each one has 4 inputs and outputs. You repeat the S-box 16 times. The definition of the S-box is given in hex in the table at the bottom. If the input of S15 is 0000, then the output is C. In bit notation, if the right-most bit is the significant bit, the output will be 1100. You're substituting some input with some output.

The inputs only affect the output, which is where there's a permutation layer afterwards represented by all the crossing lines. You're shuffling the bits. After the end of this round, you take the 64-bit output, XOR it with the next round next, and repeat the above for 31 rounds. Then you XOR with the final round key before getting the ciphertext.

![Pasted image 20211107165854.png](Pasted%20image%2020211107165854.png)

### Feistel Network
These ciphers were introduced by Horst Feistel (1915-1990). A round of a Feistel Network consists of a round function and a swap operation. The b-bit input is divided into two halves. The round function is applied to one half, and the output is XORed with the other half. Then the places of these halves are swapped. The key material is used inside the round function.

The plaintext block is divided into two. You take the right part and move it to the left, but you also pass the right part into the round function and XOR the output with the left part. Then the output of the left XOR goes to the right.

![Pasted image 20211107170809.png](Pasted%20image%2020211107170809.png)

This is a generalised Feistel, specifically the CLEFIA cipher. This cipher has a block size of 128-bits, so each line represents 32-bits. You repeat this process for as many rounds as the cipher uses. Most of the time, we don't perform the final swap in the last round because this isn't cryptographically important.

![Pasted image 20211107170949.png](Pasted%20image%2020211107170949.png)

### Comparison
Pros include:
- Feistel: encryption algorithm is identical to the decryption algorithm. Only the order of the round keys change.
- SPN: a single round affects the whole input.

Cons include:
- Feistel: a single round only affects half of the input.
- SPN: the decryption procedure requires the inverses of the substitution and permutation layers. This can produce larger clock size. You might need more gates in hardware. However, it depends on the mode of operation you use.

### Design Considerations for Different Platforms
- Hardware implementations can easily work on bits.
- Since most programming languages focus on bytes, bit operations are more costly in software.
- Cipher design also determines the number of gates required to perform encryption on hardware. The number of gates determines the area required. Therefore, we have separate lightweight cryptographic algorithms.
- Code size and required memory is not a big problem for software implementations due to fast CPUs and large amounts of RAM.

Your design should depend on the platform that will use it.

Security vs speed is another design consideration.
- Speed is generally measured as throughput.
- Adding more security measures (e.g. increasing the number of rounds) increases security but reduces speed.
- Ciphers performing well on hardware or in software may not be suitable for constrained devices for RFID systems or sensor networks due to limited memory, battery, and/or computational power, hence we also need lightweight ciphers. There's currently a lightweight crypto competition going on, which will result in one or more getting standardised by NIST.

## Data Encryption Standard (DES)
This was designed by IBM in the 1970s based on an earlier design by Feistel. It has a block size of 64-bits, a key length of 56-bits, and 16 rounds.

It's currently known as Data Encryption Algorithm (DEA) since it's no longer a standard. It became useless after the 1990s since its short key is susceptible to bruteforce attacks.

In 1976, the NSA tweaked the algorithm by changing its S-boxes but didn't explain why they made such a change. IBM people analysed this tweak and discovered an attack that breaks the initial design (T-attack). They shared their discovery with the NSA and were asked not to disclose it with the public because they'd known this attack type for some time and were using it to listen to other countries. The NSA also reduced the key length from 128-bits to 56-bits, which was even short at the time.

Biham and Shamir's rediscovery of differential cryptanalysis in the 1990s is the first public announcement of this technique. The main idea in differential cryptanalysis is to find a weakness so that a small change in the input provides an anticipated change in the output with high probability.

This is a Feistel cipher. The key schedule only contains permutations and rotations. The initial permutation (IP) has no cryptographic implications, it's just used for receiving data from the hardware. There are expansion functions and S-boxes. There are 16 rounds.

![Pasted image 20211107172741.png](Pasted%20image%2020211107172741.png)

### Exhaustive Search
An attacker that captures a single ciphertext can try to decrypt it with every possible key to check if it provides a meaningful plaintext. This is an exhaustive search or bruteforce attack. It's a generic attack (valid for every cipher). For a k-bit keyed cipher, the attacker is required to perform at most 2^k encryptions/decryptions. You can check if the ciphertext you obtained is the same as the one you captured.

The security of a block cipher is upper bounded by exhaustive search (2^k encryptions). This quantity is referred to as Time Complexity. Any weakness that makes it easier than 2^k encryptions is a cryptanalysis attack.

Even if someone could perform 2^128 in a second, they still couldn't break 2^256, which would require 2^100 years to break at that rate.

![Pasted image 20211110184345.png](Pasted%20image%2020211110184345.png)

GPUs are very good for scientific computing. You can get 10-100 times the speed compared to CPUs. FPGAs refers to Field Programmable Gate Arrays. ASICs are cost effective.

![Pasted image 20211110184525.png](Pasted%20image%2020211110184525.png)

![Pasted image 20211110184755.png](Pasted%20image%2020211110184755.png)

With modern GPUs, you can reduce this down to 10 million years. However, multiple machines speed this up, potentially allowing the bruteforce of 80-bit keys in a few days.

![Pasted image 20211110184809.png](Pasted%20image%2020211110184809.png)

### Security of DES
Biham and Shamir (1992) provided the first theoretical attack (Differential Cryptanalysis) on DES, which requires less time than an exhaustive search. However, the attack requires 2^47 chosen plaintexts to be captured, which is unrealistic in the real world.

Matsui (1994) provided the first experimental cryptanalysis of DES by introducing Linear Cryptanalysis.

In 1997, the DESCHALL Project (around 78,000 PCs connected via the internet) broke a message encrypted with DES for the first time in public.

In 1998, the EFF's DES cracker, a machine containing 1,856 custom chips, broke a DES key in 56 hours.

In 1999, DES was only allowed in legacy systems, with the industry transitioning to 3DES.

In 2002, AES (Advanced Encryption Standard) became the recommended option.

## Advanced Encryption Standard (AES)
AES/Rijndael was designed by Joan Daemen and Vincent Rijmen. It won the AES competition and was standardised in 2001 by NIST. Other finalists include SERPENT, TWOFISH, RC6, and MARS.

AES has a 128-bit block size, supports keys of 128-, 192-, or 256-bits, and uses 10, 12, or 14 rounds, depending on the key size. Basically, nobody uses 192-bit keys. There are many attacks on AES, but no known attacks are practical.

## Rounds
How many rounds should a cipher have? There's no direct answer and not even much of a scientific answer to this question because confusion and diffusion are abstract concepts.

The common belief is that increasing the number of rounds increases the security. However, this is not always true against all cryptanalysis techniques (some will work against any number of rounds), and it decreases performance. There must be a trade-off between performance and security.

The common practice by designers is to apply every known cryptanalysis technique, and if they can break n rounds, then they choose the round number r as r >= 2n to stay on the safe side (double it) because attacks always get better.

## Standardised Block Ciphers

3DES has a key size of 112-bits, and SKIPJACK has a key size of 80-bits, meaning it's only for legacy use and not recommended. AES is the most used today.

![Pasted image 20211110185252.png](Pasted%20image%2020211110185252.png)

## Problems
### Key Management
Keys must be exchanged between the communicating parties before they can use the block cipher. To communicate with n people, one must securely exchange n keys. If this is a huge number, then this is not practical.

The solutions are:
1. Use public-key encryption algorithms (e.g. RSA): slower than block ciphers, leading to performance and bandwidth issues. They're also for small messages.
2. Use key exchange algorithms (e.g. X25519): this is the recommended solution today and is used across the internet (e.g. in TLS).

### Key Storage
Storing keys directly on the hard drive or in RAM can be problematic as it's possible for an attacker to retrieve them.

This is a problem that can't be completely solved. Solutions include storing keys on a separate device and erasing keys from memory by overwriting them with zeroes.

### Message Length Hiding
The plaintext and corresponding ciphertext lengths are identical if there's no padding. If the adversary knows the context of the communication, then they can sometimes guess the plaintext from the ciphertext length.

![Pasted image 20211110190002.png](Pasted%20image%2020211110190002.png)

A solution is to add random bits to the plaintext before encryption to hide the message length. This requires more data to be transmitted.

### Key Generation
To obtain k-bit security, the k-bit key should be generated randomly. The solution is to use a cryptographically secure random number generator or key derivation function.

## Exercises
1. Many cryptanalysis techniques are statistical attacks, which involve a statistical weakness that's used as a distinguisher.
2. This is important for when we will discuss the collision resistance of hash functions.

![Pasted image 20211110190524.png](Pasted%20image%2020211110190524.png)

<details>
	<summary>Q1 answer</summary>
	Roll the dice at least 30 times (5 x the number of sides is a common rule) and record the frequency of each number?
</details>

<details>
	<summary>Q2 answer</summary>
	23 people because the comparisons are done between every possible pair of individuals.

	With 23 individuals, there are (23 × 22) / 2 = 253 pairs to consider, which is significantly over half the number of days in a year (182.5 or 183).
</details>
