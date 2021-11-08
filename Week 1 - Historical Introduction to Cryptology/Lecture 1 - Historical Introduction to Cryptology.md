# Historical Introduction to Cryptology
It's important to examine the historical progress to understand the algorithms today.

## Purpose of Cryptography
- Cryptology is about secure communication in an insecure channel.
- Cryptography is about designing secure cryptosystems.
- Cryptanalysis is about analysing (breaking) cryptosystems.

Today, the words cryptology and cryptography are used interchangeably. Cryptography solves the problems of:
- Privacy of stored data, messages, and conversations: this is done using symmetric encryption.
- Integrity of stored data, messages, and conversations: if any data changes, we can detect it. This is often done using hash functions.
- User and data authentication: this is done by public-key cryptography.
- Transaction non-repudiation: this is solved by public-key cryptography, specifically digital signature algorithms.

## Cryptosystems
- Plaintext is what you want to protect.
- A cryptosystem is a pair of algorithms that converts plaintext to ciphertext and back.
- Ciphertext is the encrypted version of the plaintext.
- The ciphertext should appear like a random sequence. We always assume someone can capture the ciphertext.

![Pasted image 20211103183459.png](Pasted%20image%2020211103183459.png)

## Monograph Ciphers
Historical ciphers are mostly pen and paper methods. The key and cryptosystem should be easy to use in practice. These are mostly based on letter substitutions. Most of the time, empty spaces and punctuation marks are removed from the ciphertext to avoid information leakage.

### Caesar Cipher
Every letter is replaced by a letter some fixed number of positions *k* down the alphabet. This was used in ancient Rome by Julius Caesar, who supposedly invented it.

The possible plaintexts are called the plaintext space. The number of possible keys is called the key space.

![Pasted image 20211103183728.png](Pasted%20image%2020211103183728.png)

There are only 25 possible keys with the Caesar cipher. This is very weak nowadays because the key space is too small, meaning it's possible to bruteforce. One known plaintext-ciphertext is enough to break the cryptosystem.

![Pasted image 20211103183909.png](Pasted%20image%2020211103183909.png)

### Affine Cipher
To increase the key space, we can use two numbers *a* and *b* as the key and encrypt as follows: *E(p, k) = a . p + b (mod 26)*.

The key space is much lower than this because we need the inverse of *a* for decryption but not every number has an inverse modular 26. Note that *.* means multiply.

![Pasted image 20211103184209.png](Pasted%20image%2020211103184209.png)

*a* can have an inverse *a^-1* in Zn if and only if greatest common divider(a, N) = 1 (e.g. 2 can't be used as *a*). There are 12 possibilities for *a* so that gcd(a, 26) = 1. Therefore, there are at most 12 x 26 = 312 usable keys.

There's repetition of J despite the plaintext letters not being the same. The person decrypting can't be sure of whether J relates to E or R.

![Pasted image 20211103184703.png](Pasted%20image%2020211103184703.png)

However, the key space is still too small, and one known plaintext-ciphertext is still enough to break the cryptosystem.

Classical cryptosystems can be categorised according to the message units that the plaintext and ciphertext are broken into:
- Monograph: single letters.
- Digraph: pairs of letters.
- Trigraph: triples of letters.
- Polygraph: longer than a single letter.

The Caesar cipher and Affine cipher are monograph ciphers. You can increase the key space by grouping a larger number of letters.

![Pasted image 20211103184902.png](Pasted%20image%2020211103184902.png)

### Simple Monoalphabetic Substitution Cipher
Every letter is replaced by another letter. The key is essentially a random alphabet. This provides a key space of 26! (factorial) for English, which is a bit bigger than 2^88/88-bit security. With the Turkish alphabet, it would become 29!. Modern computers cannot bruteforce this.

![Pasted image 20211103190022.png](Pasted%20image%2020211103190022.png)

However, there are other cryptanalysis techniques to break this cipher. The weakness is the redundancy in the language; some letters appear more than others (e.g. e and a). If you can capture the ciphertext, then you can perform frequency analysis to decrypt the message.

## Frequency Analysis
Frequency analysis was introduced by Al-Kindi in 'A Manuscript Deciphering Cryptographic Messages' in the 9th century. It's based on the redundancy of the language. For a given language, you need to find a very long text and count the frequency of each letter. This obtains the frequency of every letter for that alphabet. Historically, this had to be done by hand, but we can now use a simple program.

In English, the letter 'e' appears the most, followed by the letter 't'. For Turkish, 'a' is the most common.

Next, you need to count the letter that appears the most in the ciphertext and assume that it probably corresponds to 'e' in the plaintext. This continues. However, if the ciphertext is not long enough, then the letter that appears the most might not be 'e' but 't' and so on. It requires some trial and error and almost always doesn't match directly. If the content is on one topic (e.g. cryptography), then a different letter (e.g. 'c') may be extremely common.

This is a very good cryptanalysis technique. Today's modern cryptanalysis techniques are also statistical attacks. This gives you a basic idea of modern techniques.

## Digraph Ciphers
### Playfair Cipher
This was invented by Charles Wheatstone in 1854. It was the first literal digraph substitution cipher.

It uses a 5 x 5 table containing a keyword and the rest of the alphabet. Memorising the keyword (key) and 4 simple rules is enough to use Playfair. Ironically, the British Foreign Office rejected it because of its perceived complexity, even though Wheatstone offered to demonstrate that 3/4 boys in a nearby school could learn how to use it in 15 minutes. The rest of the world started using it. Lord Playfair promoted its use, hence the name.

Modern ciphers have algorithms that help us use keys, which is called the key schedule. You write the keyword at the top and fill in the table with the remaining unused letters. The person decrypting must decide whether a letter corresponds to 'i' or 'j'.

![Pasted image 20211104203024.png](Pasted%20image%2020211104203024.png)

Here are the rules:
1. If both letters are the same (or only one letter is left), add an 'X' after the first letter.
2. If the letters appear on the same row of your table, replace them with the letters to their immediate right respectively (wrapping around to the left side of the row if a letter in the original pair was on the right side of the row).
3. If the letters appear on the same column of your table, replace them with the letters immediately below respectively (wrapping around to the top side of the column if a letter in the original pair was on the bottom side of the column).
4. If the letters are not on the same row or column, replace them with the letters on the same row respectively but at the other pair of corners of the rectangle defined by the original pair. The order is important - the first letter of the encrypted pair is the one that lies on the same row as the first letter of the plaintext pair.

![Pasted image 20211104203423.png](Pasted%20image%2020211104203423.png)

Since this is a digraph cipher, you need to group the plaintext letters into pairs. The rules say to replace 'C' with the letter to the right ('R') and 'Y' with the letter to the right ('P').

![Pasted image 20211104203609.png](Pasted%20image%2020211104203609.png)

Because 'B' and 'E' are not on the same row, you have to create a rectangle. 'L' and 'I' are on the opposite diagonals/sides.

![Pasted image 20211104203659.png](Pasted%20image%2020211104203659.png)

Next, 'R' and 'S' are not on the same row, meaning we do the same.

![Pasted image 20211104203802.png](Pasted%20image%2020211104203802.png)

![Pasted image 20211104203831.png](Pasted%20image%2020211104203831.png)

![Pasted image 20211104203842.png](Pasted%20image%2020211104203842.png)

Here, 'I' and 'T' are on the same column, so you have to replace them with the letter below.

![Pasted image 20211104203905.png](Pasted%20image%2020211104203905.png)

Then the last one is a rectangle again.

![Pasted image 20211104203920.png](Pasted%20image%2020211104203920.png)

As far as I know, it was used in WW1. This cipher was followed up by the four-square and two-square ciphers, which were slightly more cumbersome but had marginally better security.

### Frequency Analysis Countermeasures
- Use polygraphic substitution. This idea led to the development of block ciphers.
- Playfair or digraph ciphers are still vulnerable to two-letter frequency analysis.
- Computers allow frequency analysis on short polygraphic substitution (e.g. Playfair).
- 8 character simple substitution systems are probably secure. However, designing such a cipher is not easy, and it won't be usable via pen and paper. It won't be easy to use like Playfair. Longer substitution also leads to a key too long to use/store in practice.

## Kerkchkhoffs's Principle (1883)
A cipher must not be required to be secret. It must be able to fall into the hands of the enemy without inconvenience. In other words, the security of the system must rest entirely on the secrecy of the key. Security through obscurity (hiding things) has failed numerous times. Always assume that the cipher can fall into the hands of the enemy.

Claude Shannon also said, 'The enemy knows the system'. This is realistic. The three Bs of cryptography are:
1. Bribe
2. Burglary
3. Blackmail

This principle is important for the rest of the course and all academic work related to cryptography.

## The Unbreakable Cipher
The one-time pad is unbreakable if the one-time pad is truly random and only used once. It involves generating a very long sequence of random bits. You then XOR the plaintext and the one-time pad to get the ciphertext. For decryption, you XOR the ciphertext and the one-time pad to get the plaintext.

XOR means exclusive OR. This is equivalent to addition in modulo 2. Each column affects the same column.

![Pasted image 20211105201522.png](Pasted%20image%2020211105201522.png)

If you encrypt a plaintext with a one-time pad and then another plaintext with the same one-time pad, then an attacker who captured both ciphertexts can capture parts of/all of the plaintext.

The one-time pad provides perfect secrecy (the ciphertext provides no information about the plaintext). It's usually printed on a single page with very small letters, hence the name one-time pad. Instead of working with bits, one can work on letters or characters.

The limitations include:
- The key is too long because it's the same length as the plaintext.
- Key distribution is tricky because you can only use a key once.
- The key must be truly random.

Therefore, it's not practical.

### Key Reuse
When you do the XOR, the Ks get cancelled out, leaving P1 XOR P2 (the XOR of the plaintexts). Then you can use statistical techniques to capture at least parts of P1 and P2.

![Pasted image 20211105202049.png](Pasted%20image%2020211105202049.png)

One-time pads have been broken in past, which is why stream ciphers were later developed.

![Pasted image 20211105202245.png](Pasted%20image%2020211105202245.png)

## Cipher Machines
Technological advancements replaced pen and paper methods with machines. The most famous example is Enigma. An Enigma Machine is an electro-mechanical rotor cipher machine. It was used in the 20th century by military and commercial sectors like banking.

It generates a polyalphabetic substitution cipher. Cryptanalysis of Enigma by British mathematicians/cryptologists, with the initial help of Polish cryptologists), changed the course of WW2.

You write your plaintext using the typewriter, which transforms the letters into other letters due to the rotors. The position of the rotors changes. The key is the position of the rotors.

![Pasted image 20211105202816.png](Pasted%20image%2020211105202816.png)

### Cryptanalysis of Enigma
A letter is never mapped to itself (it's an impossible event), which may have been a precaution. However, this led to the break of the Enigma machine.

In WW2, they captured ciphertexts and knew that at some point the enemy would send 'nothing to report'. They wrote out the expected message aligned to different parts of the ciphertext. If a letter matched the letter in the ciphertext, then they knew that the rotors were not set there. Once you can decrypt that part of the message, you can set the rotors correctly for the rest of the message to check whether it was valid German.

![Pasted image 20211105203122.png](Pasted%20image%2020211105203122.png)

Germans constantly modified their machines during the war, and their latest cipher machines eluded allied cryptologists.

## Colossus
This cryptanalysis effort led to the technological advancement called the Colossus computer. This was the first programmable electronic digital computer, made in 1943. It was developed by British codebreakers at Bletchley Park to cryptanalyse the Lorenz cipher. Alan Turing's use of probability in cryptanalysis contribute to its design.

It optically reads a paper tape and then applies programmable logical functions to the bits of the key and ciphertext characters, counting how often the function returned 'false'. The British destroyed the computer after WW2 to prevent it from being leaked. It was reconstructed in 2007 and is available in a museum.

![Pasted image 20211105203747.png](Pasted%20image%2020211105203747.png)
