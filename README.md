# Long Long Encrypt
An simple RSA encryption.<br>
Writeup By [226914011(Nori_egg)](https://github.com/226914011).

## Description
![img](https://i.imgur.com/GRGZewq.png)<br>
**Files**: [encrypt.py](https://api.ctf.sg/file?id=ckm3krkb80gsw0880y0z22pyf&name=encrypt.py)<br>
**Files**: [txt.enc](https://api.ctf.sg/file?id=ckm3krkeq0gtf0880li3jzboy&name=txt.enc)

## Solution
As I'm a newbie to Crypto, please dun be rude to me if I mess up sth.

### Part I: Is it RSA?
As we can get n, e, c from the txt.enc file, I guess it's an RSA-like encryption method.<br>
And the encryption is here:<br>
```python
def is_prime(n):
    # Implement some prime checking function here
    return True  # Placeholder

def bti(s):
    out = 0
    for i in s:
        out = (out * 256) + ord(i)
    return out

def encrypt():
    fp = open('params.txt', 'r')
    p = int(fp.readline())
    q = int(fp.readline())
    
    assert is_prime(p)
    assert is_prime(q)

    n = p**q
    e = 1000000000000000003

    assert (p-1) % e != 0

    fc = open('flag.txt', 'r')
    m = bti(fc.read())

    if (m > n):
        print("PANIC")
        return

    c = pow(m, e, n)

    print(f'n = {n}')
    print(f'e = {e}')
    print(f'c = {c}')


if __name__ == '__main__':
    encrypt()
```
From the script above, few pieces of information are given.
1. The encrypt function will check if p and q are primes<br>
2. `n` is equal to `p` to the power of `q`<br>
3. `e` is equal to `1000000000000000003`<br>
4. Chipertext is using classic RSA encrpytion `c = pow(m, e, n)`<br>

Okay, now I can decrypt it back to plaintext.

### Part II: Decryption
To decrypt the chipertext(`ct` or `c` in this challenge) back to plaintext(`pt` or `m` in this challenge),<br>
I need to use `pt = pow(ct,d,n)` to encrypt. As I already got `ct` and `n`, so I need to get `d`.<br>
To get `d`, there is an easy way through `Crypto` module in python:
```python
from Crypto.Util.number import inverse,long_to_bytes
d = inverse(e, phi)
```
Now I got another problem. I dun have `φ(n)`(phi).<br>
There are two ways, one easy and no-brainer, one using Math to solve.<br>

---

#### Easy way: [Integer factorization calculator](https://www.alpertron.com.ar/ECM.HTM)
This [Integer factorization calculator](https://www.alpertron.com.ar/ECM.HTM) is my favorite site for solving RSA cuz it will automatically calculate `p`, `q` and `φ(n)`(Euler's totient) for me.
Now I have all I need. Easy peasy lemon squeezy.<br>

---

#### Math way: [Euler's product formula](https://en.wikipedia.org/wiki/Euler%27s_totient_function#Euler%27s_product_formula)
As n is p to the power of q instead of p*q, that means I have to get `p` and `q` first and use [Euler's product formula](https://en.wikipedia.org/wiki/Euler%27s_totient_function#Euler%27s_product_formula) to get `φ(n)`.<br>
I need to do integer factorization first, and get `p = 28375637489003756813`&`q = 1709`.<br>
Then, we know `φ(n) = φ(p^q) = p^(q-1)*(p-1)`, so we can get φ(n) by script:
```python
phi = p**(q-1) * (p-1)
```
---

#### Full script : [RSA.py](https://github.com/226914011/Long-Long-Encrypt-Writeup/blob/main/RSA.py)

### Part III: Wait few hours to get the flag
```
Every National Day, we remind ourselves that Singapore is a nation whose story is worth celebrating, whose history is worth cherishing, and whose future is worth building.

When we hold the National Day Parade at the Padang, or at the Float@Marina Bay, the audience can see the Marina Bay skyline in the background. Marina Bay used to be open sea, but we reclaimed the land around it, turned the ocean into a reservoir, and progressively built on the new land, to create an outstanding and vibrant downtown. Looking out across the Bay, you can immediately see how far Singapore has come, and imagine the possibility and promise that Singapore holds.

Today, when National Service recruits complete their Basic Military Training (BMT), they gather their families and friends at the Float and hold their passing out parade there. It helps them to understand what they are defending, and why generations of national servicemen have been willing to serve and sacrifice, to defend this metropolis that we have built together, and also the society that created all this: Our families. Our friends. Our lives. Our future.

Three years ago, I announced that we would rebuild the Float into a permanent space to commemorate National Service, and the central role that NS plays in Singapore, and that we would name this space, NS Square. The design contract has been awarded. This is how it will look like. The development will be aligned on a central axis, with The Promontory on the opposite side of the Bay. The platform will become a red dot, shining bright in our city. NS Square will be the central focus of our new downtown. We will build a gallery there to showcase our National Service story, and honour the contributions of national servicemen past and present. NS Square will also be a community space for everyone, young and old, to enjoy. And of course, we will continue to hold our National Day Parades there.

This National Day, as we celebrate across the island, we will be saying the Pledge and singing Majulah Singapura in unison@ not aloud as usual because of COVID-19, but in our hearts, with more feeling than ever.

We have dreams to realise, and goals to reach for. Let us show the world that whatever the challenges, Singaporeans will stay united, and prevail once more.

I wish everyone a very happy National Day! The flag is CTFSG{mY_fEll0W_s1Ngap0r3aNs}.
```
### Part VI: Final thoughts
It was really a great challenge for beginners like me. Thank you for reading this write-up.<br>
If you have any questions about this challenge, please feel free to contact me while GitHub/discord(226914011#2099)
