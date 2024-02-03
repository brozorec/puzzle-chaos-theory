## Puzzle
*Bob designed a new one time scheme, that's based on the tried and true method of encrypt + sign. He combined ElGamal encryption with BLS signatures in a clever way, such that you use pairings to verify the encrypted message was not tampered with. Alice, then, figured out a way to reveal the plaintexts...*




## Background:

### ElGamal encryption
The ElGamal encryption system is a public-key encryption algorithm based on the Diffie-Hellman key exchange protocol. The system allows two parties to exchange messages securely without having previously shared a secret key. It works by first generating public key $Pk\$, private  key $sk\$ pair. To encrypt a message $m\$ the sender selects a random number $k$ and computes $c_1 = g^k\ mod p$ and $c_2 = m * rPk^k\ mod p$ where $rPk\$ is the receiver public key.



### ElGamal with elliptic curve
Translating ElGamal encryption to elliptic curves involves leveraging the properties of elliptic curve cryptography (ECC) to implement the ElGamal encryption scheme. The private key or random integer $k\$ is as $1 < k < n\$ where n is the order of the curve. The public key is generated as $Pk = kG\$ where $G\$ is the generator of the curve group.

To encrypt a message $m\$ which has been encoded as ec point $P\$ the sender randomly selects an integer $k\$ and computes two points $C_1 = kG\$ and $C_2 = m + kPk\$

### Puzzle setup
In this puzzle message is encrypted using senders public key which is $C_1\$ component and $C_2\$ component of Elgamal made up of multiplying receiver's public key with sender's private key (random integer $k\$) and adding to the message. 
Then this encrypted message is signed by multiplying `hashTocurve` of the encrypted message and sender's private key.

Since this Elgamal encryption is set up using elliptic curve, billinear mapping is used to verify the signature. 

A blob object is given with ElGamal ciphertexts, signature, sender's and receiver's public key. The target group is also given with message space of 10 elliptic curve points.

Goal is to find out the index from the message space that was encrypted in the blob. 


## Solution
In this puzzle we can see that Bob has used same random number $k\$ is used for both $C_1\$ and $C_2\$. This means for different messages Bob has used the same $C_1\$ component and the $kPk$ part  of both $C_2\$ is the same for different $m$ we can compute the ratio of any two ciphertext as below after $kPk$, component is factored out.

$m_1/m_2 = c_2^1/c_2^2\$

Since you cannot directly divide points on an elliptic curve as you would with numbers in a field, the concept of a "ratio" is converted through point subtraction, using the additive homomorphism of the elliptic curve group.On elliptic curves this translates to ,

$m_1 = C_1 - C_2 + m_2 \$


Since there are 10 message with their ec points given, we can iterate through the messages and check which message passes the bilinear pairing check.

Below is the `Rust` code for the attack.

```
    for (i, msg) in messages.iter().enumerate() {
        let c_prime = blob.c.1 - msg.0;
        let hsh = blob.c.hash_to_curve();

        let lhs = { Bls12_381::pairing(blob.rec_pk, blob.s) };
        let rhs = { Bls12_381::pairing(c_prime, hsh) };

        if lhs == rhs {
            println!("Index of the encrypted message: {}", i);
        }
    }

```




