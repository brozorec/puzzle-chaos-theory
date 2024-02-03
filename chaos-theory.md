## Puzzle
Bob designed a new one time scheme, that's based on the tried and true method of encrypt + sign. He combined ElGamal encryption with BLS signatures in a clever way, such that you use pairings to verify the encrypted message was not tampered with. Alice, then, figured out a way to reveal the plaintexts...






## Background:

### ElGamal encryption
The ElGamal encryption system is a public-key encryption algorithm based on the Diffie-Hellman key exchange protocol. The system allows two parties to exchange messages securely without having previously shared a secret key. It works by first generating public key $$Pk$$, private  key $$sk$$ pair. To encrypt a message $$m$$ the sender selects a random number $$k$$ and computes $$c_1 = g^k mod p$$ and $$c_2 = m * rPk^k mod p$$ where $$rPk$$ is the receiver public key.



### ElGamal with elliptic curve


