Moving Window FDH
-----------------

This is an experimental Full Domain Hash (FDH) that is constructed of a moving-window applied against an extendable ouput (XOF) hash function.

This is experiemental, and has not been formally shown to be secure.

### Background and Rational

A Full Domain Hash is a hash function who's output lies within a specific domain, generally a range `(m, n]`, where `m` is the minimum and `n` is the maximum. 

XOF hash functions can be used directly as a full domain hash when `n` is exactly equal to the highest possible value of an XOF at a give output length. However, this is often not the case. For example, an FDH for use in RSA requires that the length of the FDH be equal to the bit-length of the modulus. However, the *value* of the FDH must lie below the value of the modulus. 

This is traditionally accomplished by constructing an FDH out of a fixed-length hash function and extending it as follows:

```
cycles=(target_length) / (digest_length) + 1
FDH(M) = HASH(M||(SV + 0)) || HASH(M||(SV + 0)) || ... || HASH(M||(SV + cycles−1))

SV = 0
digest = FDH(message)
while digest > n OR digest <= m
    sv++
    digest = FDH(message, SV)
return digest
```

This traditional method works, but is computationally expensive because it requires recomputing `HASH(M||(IV + 0)` multiple times with every iteration of `IV`.

### Description

The Moving Window Full Domain Hash (MWFDH) is constructed as follows:

Pseudocode:
```
H = XOF(message);
digest = H.read_bits(target_length)
while digest > n OR digest <= m
    digest = digest[1..] || H.read_bits(1) // equivilent to a bitshift
return digest
```

Each iteration of the MWFDH requires only a single bit shift. 