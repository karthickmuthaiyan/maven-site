title: Developers centre - Making GPG Keys
author: Vincent Siveton
date: 2006-10-01

<!--
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
-->

## Introduction


 You need to add your GPG keys in [https://svn.apache.org/repos/asf/maven/project/KEYS](https://svn.apache.org/repos/asf/maven/project/KEYS) before a release. Here are some useful [GnuPG](http://www.gnupg.org/) commands to generate your Keys.


### gpg --gen-key



```
>gpg --gen-key
gpg (GnuPG) 1.4.5; Copyright (C) 2006 Free Software Foundation, Inc.
This program comes with ABSOLUTELY NO WARRANTY.
This is free software, and you are welcome to redistribute it
under certain conditions. See the file COPYING for details.

gpg: keyring `C:/Documents and Settings/Siveton Vincent/Application Data/gnupg\secring.gpg'
created
gpg: keyring `C:/Documents and Settings/Siveton Vincent/Application Data/gnupg\pubring.gpg'
created
Please select what kind of key you want:
   (1) DSA and Elgamal (default)
   (2) DSA (sign only)
   (5) RSA (sign only)
Your selection? 1
DSA keypair will have 1024 bits.
ELG-E keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 2048
Requested keysize is 2048 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 0
Key does not expire at all
Is this correct? (y/N) y

You need a user ID to identify your key; the software constructs the user ID
from the Real Name, Comment and Email Address in this form:
    "Heinrich Heine (Der Dichter) <heinrichh@duesseldorf.de>"

Real name: Vincent Siveton
Email address: vsiveton@apache.org
Comment:
You selected this USER-ID:
    "Vincent Siveton <vsiveton@apache.org>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
You need a Passphrase to protect your secret key.

You don't want a passphrase - this is probably a *bad* idea!
I will do it anyway.  You can change your passphrase at any time,
using this program with the option "--edit-key".

We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
++++++++++++++++++++.++++++++++..+++++++++++++++++++++++++++++++++++++++++++++++
+++.+++++++++++++++.++++++++++++++++++++..+++++++++++++++>++++++++++............
.........................+++++
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
.+++++++++++++++..++++++++++++++++++++....+++++.++++++++++.++++++++++.++++++++++
+++++.+++++++++++++++++++++++++++++++++++.+++++.++++++++++++++++++++++++++++++>+
+++++++++>+++++>+++++......................................................>++++
+......<.+++++........................+++++^^^
gpg: C:/Documents and Settings/Siveton Vincent/Application Data/gnupg\trustdb.gpg: trustdb
created
gpg: key 07DDB702 marked as ultimately trusted
public and secret key created and signed.

gpg: checking the trustdb
gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
pub   1024D/07DDB702 2006-10-10
      Key fingerprint = 71F6 F555 8A61 71C4 330D  B868 84F4 D470 07DD B702
uid                  Vincent Siveton <vsiveton@apache.org>
sub   2048g/D2814A59 2006-10-10

```


### gpg --list-sigs && gpg --armor --export



```
>gpg --list-sigs "Vincent Siveton" && gpg --armor --export "Vincent Siveton"
pub   1024D/07DDB702 2006-10-10
uid                  Vincent Siveton <vsiveton@apache.org>
sig 3        07DDB702 2006-10-10  Vincent Siveton <vsiveton@apache.org>
sub   2048g/D2814A59 2006-10-10
sig          07DDB702 2006-10-10  Vincent Siveton <vsiveton@apache.org>

```



## Version: GnuPG v1.4.5 (MingW32)



## mQGiBEUrnAsRBACQDiYGc1IQmkENLO9iznBg8otGPEbzqQozT5tsip5mB30f6Mc/ uuLxJkLdna7Ul3goIXDtCeLJq38gHvruNtVNR6S+juJFkd5sLEH8UJ18PbKuo/9I KGlzjtCYUUDC48czRr0efhqd54NH8ydNdpaZ76NGPPYfpXtk7kKgH/nPiwCgxozK IG2frMuWIvdFafbqdAl7y/sD/1Csf0r9jtHEeXOuyhm8jCGrSwzLbHUGKPUQP37P ajECHoWp6HnvHEEEpgVl+UjfZvrcVhzUoP+3r5HAugqERfkzK8AWc7qjIRjf64kU sjvto31G2KYz17Y8K9y4LkRkUspu8uw903pKnW/QELg4vvPVaEYpVVIdS6+cUreu V0hOA/4tW7T/GpzSbQmjvnIRQ7GVHbQeXsANwrS6NmGYIxafN9P9dfHV+eUieTu6 rvMP9coOhTYyEKZksrXw2MmXx5SzgxsXT0g4wDXbwxPYFfIdGUzFMobnVXiZ3G8l JEl9cML0cg3ZL1SoDmVf2iG3e3Yxxsne4AE1SU+0bbq0t7rqALQlVmluY2VudCBT aXZldG9uIDx2c2l2ZXRvbkBhcGFjaGUub3JnPohgBBMRAgAgBQJFK5wLAhsDBgsJ CAcDAgQVAggDBBYCAwECHgECF4AACgkQhPTUcAfdtwLP3gCbB/V1afp8hzxgirCS d2r6zCkJQ2IAoLKD/RIkkerNintYzrubJliJKBsRuQINBEUrnBgQCAD1+Sx+sBDL 1XCDtxQGsrZmMnJJVK/w4TPa/8weJkuZ1GSpINOjInmqESuehvCLoOoyfcuDVXlR PUZhKZLPEKfJlFptGNK19oTO/CoQN+SJLwR41FoumsBaf1YSSRpAukyx2J6cUxqf uWrK/T8PmgDw4YzmY96tev//41eZ5tSOxpoUM8ypnTaShtS9pjgHijEG0b7wBqeU e1OGOiLHgKyjEJUmlTaLm1SxJ84eq0uAvYb+rb/QoWWLpjvr2/mo1kzUvCPgo3fh kgOxCxsC9QD836Mi5HFK6CRYU3yAFu5+/jM+LJzELy3u7uMuOSP6yuiK8WXopdbN WHOiJQfdc2gTAAMFCADdljjAG7L+8de6JzsEduKErKqWlTEWa99n1knaGKzdUUOP WrKxwqgI6PAJbxOfG4vBdDa6M6+nySJDMybQsOCFyNx91/7jYkgkmv8Jkt8CTW4z P4HKlFYMAFpU95ftpTAAMAlr+t+nZRTHi94/VHMv4yLGzB/xapbzToHKuUt1Yqom Ncio5px7RVoicn13/i/GeY72fIdC2LRGo6PXlmmDQemoAnUw0RJoEtzapb0j/tWd BdAtQQX/Ks7qhk4aDDHGgO+CdHAB8PLHDpMpUX5Zc9JX1xhyJcS8d/LPUpXtt9WN eekqDpx+jNmySJr6os7rPAkjx6jDUvHPiuKdT4aviEkEGBECAAkFAkUrnBgCGwwA CgkQhPTUcAfdtwJL9ACgmLuDxE+oZaMhyFSmXWN0fM36Bd8AoLYrvwydB9+nNnhJ 85TjkMPTgjp9 =Hg4C -----END PGP PUBLIC KEY BLOCK----- ----------------


 You need to append this result to [https://svn.apache.org/repos/asf/maven/project/KEYS](https://svn.apache.org/repos/asf/maven/project/KEYS).


 You also need to upload your key to the public server: [http://pgp.mit.edu/](http://pgp.mit.edu/) by copying the same you appended in the text field and submit. You can ensure by searching your email in key search engine.


### gpg --fingerprint



```
>gpg --fingerprint vsiveton
pub   1024D/07DDB702 2006-10-10     
      Key fingerprint = 0000 0000 0000 0000 0000  0000 0000 0000 0000 0000
uid                  Vincent Siveton <vsiveton@apache.org>
sub   2048g/D2814A59 2006-10-10
```

 Go to [https://id.apache.org](https://id.apache.org), log in and fill `OpenPGP Public Key Primary Fingerprint:` with the value of `Key fingerprint`.


 You can read more about [Checksums And Signatures](https://www.apache.org/dev/release-signing.html#faq).



