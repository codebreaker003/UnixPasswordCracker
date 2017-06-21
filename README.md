# UnixPasswordCracker

## Introduction:

This is a 20 year old unix password cracker. It uses crypt module of python to crack the passwords.
It can also crack passwords from shadow file of your linux machine which is located in /etc/shadow.

## Prerequisites

You should have a basic knowledge of hashing and cryptography
### understanding the components of a shadow file:

This is a sample shadow file text
```dedsec:$6$9PUOn12d$NdIVCV32guKuwx/habJ2DokF1.jUWBcV5U8OPI3v0PVIjoyFAIEc95mKvINhkFiz3O.Wsoxw97UjX8SEzB6240:17337:0:99999:7:::``` 

Let's now dive into the components of this encrypted txt
* First part (username): the first part is: ```dedsec```, this is defines your username.

* Second Part (hash): This is ```$6$``` in the text. This basically defines which algorithm is being used for encrypting password
  * $1 = MD5 hashing algorithm.
  * $2 = Blowfish hashing algorithm.
  * $2a = eksblowfish hashing algorithm.
  * $5 = SHA-256 hashing algorithm.
  * $6 = SHA-512 hashing algorithm.

Modern unix-based machines use $6 i.e SHA-512 algorithms

![Image of Hashing](https://www.ssl2buy.com/wiki/wp-content/uploads/2015/12/hashing-vs-encryption.png)

The above picture should give you an idea how hashing works and how is it different from encryption and decryption.
If you have some difficulty understanding this then you can always google your problem. I am sure you will get a solution.

* Third Part (salt): This is very very important! This a 8 byte long text which is used to prevent cracking of passwords using rainbow tables. In the text salt is : ```9PUOn12d```

* Fourth Part (password): Now comes the main part, the PASSWORD!!. password in the text is : ```NdIVCV32guKuwx/habJ2DokF1.jUWBcV5U8OPI3v0PVIjoyFAIEc95mKvINhkFiz3O.Wsoxw97UjX8SEzB6240```

Other fields are not required..

For more information visit [This Site](http://www.yourownlinux.com/2015/08/etc-shadow-file-format-in-linux-explained.html).
