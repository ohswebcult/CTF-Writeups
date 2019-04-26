# Neo, Smith & Zion, Oh My!
The question uses Pretty Good Privacy

Given:\
neo1.png.asc --> PGP MESSAGE\
agent-smith.sec --> PGP PRIVATE KEY\
agent-smith.pub --> PGP PUBLIC KEY

```shell
gpg --import neo1.png.asc
```

It will prompt you to `Please enter the passphrase to import the OpenPGP secret key:`\
The passphrase is `password`

```shell
gpg neo1.png.asc
strings neo1.png | tail
```
After executing the above command, we can spot the flag **ZION@62.537444,113.994389**
