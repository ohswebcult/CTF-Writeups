# Fermat's Little Theorem
# WIP

We reccomend that everyone reads the Wikipedia entry on RSA - It's reasonably comprehensive, well written and the psuedocode is easy to understand. 

First, we recall that Fermat's Little Theorem is a primality test, after reading the RSA wikipedia entry. And since we know that if you can break the factorization of the public key, you can find the private key.

We could write a program to use Fermat's after extracting relevant numbers from the public key with openssl, or we could use google to find a github repo that does this for us.

A google search reveals: https://github.com/Ganapati/RsaCtfTool 

We run RSA CTF tool on the relevant files:
./RsaCtfTool.py --publickey ./key.pub --uncipherfile ./cipher.asc

But we end up with extra data along with the flag. Because there's no flag format, this is a way to get stuck.

Since we didn't review the documentation to just extract the private key, we edited the program to dump the private key. 

After extracting the private key, we used openssl to decrypt the data. We end up with just the base64-looking portion of the data.
