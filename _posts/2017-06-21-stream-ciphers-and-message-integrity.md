---
layout: post
title: Stream ciphers and message integrity
---

One of my favorite things to do around Christmas is to participate in [SANS Holiday Hack Challenges](https://holidayhackchallenge.com/). These challenges are a fun way to learn and practice hacking skills. They are released once a year around Christmas and cover a broad range of technologies, tools and hacking techniques. Aside from the high quality of the exercises in an extremely gamified environment, these challenges are: 1) absolutely free; 2) available indefinitely, so folks can play year-round, going back to past challenges as they wish.

The 2016 challenge included many tasks such as reverse engineering a mobile application, Linux hacking, password cracking, network dump analysis and, of course, web application hacking. One of the web sites had a vulnerability allowing an attacker to modify their session cookie to escalate privileges from guest to administrator. This was trivial to accomplish after getting possession of the application’s source code along with the hardcoded encryption key. But what if the key was not known?

This article will demonstrate a practical attack on RC4 ciphertext to make controlled changes in the decrypted plaintext **without** knowing the encryption key. The technique relies on certain properties of stream ciphers.

<!--more-->

## Stream ciphers overview

At a high level, stream cipher encryption consists of two steps:

1. Generate a [virtually infinite] pseudorandom stream of bits (keystream) determined by a fixed length key.
2. Combine the keystream with the plaintext using the exclusive or (XOR) operation.

Due to the properties of the XOR operation, the decryption process is exactly the same as encryption – a simple XOR of the keystream and the ciphertext.

For example, let’s assume we have key _k_ and plaintext _p_ of length _n_. Then our encryption will result in the ciphertext _c_ as follows:

1. _k → r_ (_k_ is converted to pseudorandom stream _r_ of length _n_)
2. _c = p ⊕ r_

And decryption will look like:

1. _k → r_ (_k_ is converted to pseudorandom stream _r_ of length _n_)
2. _p = c ⊕ r_


## Throw your key away after use

After little analysis, it becomes clear why one should not reuse the key. Suppose you encrypted two messages, _p1_ and _p2_, using the same key _k_:

1. _k → r_
2. _c1 = p1 ⊕ r_
3. _c2 = p2 ⊕ r_

If an attacker intercepted both ciphertexts, they can perform the following operation:

_c1 ⊕ c2 = (p1 ⊕ r) ⊕ (p2 ⊕ r) = p1 ⊕ p2 ⊕ r ⊕ r = p1 ⊕ p2_

This is possible because of a few properties of XOR:

1. XOR is associative, meaning we can change the order of operations without affecting the outcome.
2. XORing anything with itself equals 0.
3. XORing anything with 0 gives you back the same result.

Reusing keys in practice means that ciphertexts alone will reveal relationships between plaintexts, and in many cases further analysis of such relationship can uncover both plaintexts.


## No integrity = no security

An encrypted message needs additional integrity protection. Otherwise, it can be easily modified by a man-in-the-middle. If an attacker knows a certain portion of the message, they can change it to any other value without knowing the key. A classic example is an electronic funds transfer transaction containing the encrypted string “$1000.00”. An attacker would then modify the ciphertext _c_ as follows:

_c1 = c ⊕ (“1000.00” ⊕ “9999.99”)_

Since _c = “1000.00” ⊕ r_, the modified ciphertext is really:

_c1 = c ⊕ (“1000.00” ⊕ “9999.99”) = (“1000.00” ⊕ r) ⊕ (“1000.00” ⊕ “9999.99”) = “9999.99” ⊕ r_

When the recipient decrypts the modified ciphertext, they get:

_c1 ⊕ r = (“9999.99” ⊕ r) ⊕ r = “9999.99”_

This is known as a **bit flipping** attack.


## Get ready for privesc

Enough with the theory already, let’s do some hacking!

Our target application is the mobile analytics server at [https://analytics.northpolewonderland.com](https://analytics.northpolewonderland.com):

![screenshot](/assets/2017-06-21-stream-ciphers-and-message-integrity/01-login.jpg)

There is a few things we need to do before privilege escalation


## Let the guest in

By reverse engineering the SantaGram mobile application (out of scope of this write-up), we learn the guest credentials:

- username: guest
- password: busyreindeer78

Log in and see:

![screenshot](/assets/2017-06-21-stream-ciphers-and-message-integrity/02-guest.jpg)

Let’s go to the browser’s developer tools and look at the AUTH cookie (this is the only cookie set by this application). I use Chrome so my path is F12 → Application → Storage → Cookies:

![screenshot](/assets/2017-06-21-stream-ciphers-and-message-integrity/03-cookie.png)

The full value of my cookie is below (yours will be slightly different):

```
82532b2136348aaa1fa7dd2243da1cc9fb13037c49259e5ed70768d4e9baa1c90b96f9e8bfa12882f878bb7fc49f0953b14348637bec
```


## Steal the source

Another part of the challenge was to pull the web application’s source code from the server. The particular method is out of scope of this write-up, so feel free to take it as an exercise.

Hint: there is a Git repo at [https://analytics.northpolewonderland.com/.git/](https://analytics.northpolewonderland.com/.git/)


## The cookie recipe

Let’s take a look at _login.php_:

```
$auth = encrypt(json_encode([
 'username' => $_POST['username'],
 'date' => date(DateTime::ISO8601),
]));

setcookie('AUTH', bin2hex($auth));
```

We can see that the AUTH cookie is an encrypted JSON string in the following format:

```
{"username":"<username>","date":"YYYY-MM-DDTHH:MM:SS+HHMM"}
```

We will find the _encrypt()_ function along with the hardcoded key in _crypto.php_:

```
<?php
  define('KEY', "\x61\x17\xa4\x95\xbf\x3d\xd7\xcd\x2e\x0d\x8b\xcb\x9f\x79\xe1\xdc");

  function encrypt($data) {
    return mcrypt_encrypt(MCRYPT_ARCFOUR, KEY, $data, 'stream');
  }

  function decrypt($data) {
    return mcrypt_decrypt(MCRYPT_ARCFOUR, KEY, $data, 'stream');
  }
?>
```

MCRYPT_ARCFOUR simply means RC4. Funny name.

RC4 is a **stream cipher**.


## Administrator’s name

On a login attempt, the site returns different results for non-existing user and wrong password, which enables user enumeration attack. By trivial user enumeration, we can find that username _administrator_ exists.

Or we can just get it from _db.php_:

```
function check_access($db, $username, $users) {
  # Allow administrator to access any page
  if($username == 'administrator') {
    return;
  }
```


## Privesc with known key

Since we know both the encryption algorithm and the key, we can decrypt our AUTH cookie:

```
$ echo 82532b2136348aaa1fa7dd2243da1cc9fb13037c49259e5ed70768d4e9baa1c90b96f9e8bfa12882f878bb7fc49f0953b14348637bec | xxd -r -p | openssl rc4 -K "6117a495bf3dd7cd2e0d8bcb9f79e1dc"

{"username":"guest","date":"2017-05-11T20:07:58+0000"}
```

Let's replace _guest_ with _administrator_ and perform the opposite operation (encryption):

```
$ echo -n '{"username":"administrator","date":"2017-05-11T20:07:58+0000"}' | openssl rc4 -K "6117a495bf3dd7cd2e0d8bcb9f79e1dc" | xxd -p -c 256

82532b2136348aaa1fa7dd2243dc0dc1e10948231f339e5edd5770daf9eef18a4384f6e7bca04d87e572be65cf9b654ab149486463a40b63b71976884152
```

Now change the cookie value in the browser:

![screenshot](/assets/2017-06-21-stream-ciphers-and-message-integrity/04-admin-cookie.png)

And now I’m in as an administrator, having access to the Edit function:

![screenshot](/assets/2017-06-21-stream-ciphers-and-message-integrity/05-edit.jpg)

Knowing the key, this was pretty trivial, but let’s see if we can do better.


## No key? No problem!

Let’s imagine the application was written in a more secure way, with the key stored in a protected location rather than being hardcoded in a PHP file. Could we still hack this?

First, note the date part of the AUTH cookie is never checked by the application, so it can have literally any value or, perhaps, be completely omitted.

With that knowledge, we’ll use the **bit flipping attack** (see above) to modify the portion of the encrypted AUTH cookie and replace _user_ with _administrator_, while not caring about the _date_ value at all.

We are going to bake our new cookie using the following recipe:

_new cookie = guest AUTH cookie ⊕ guest JSON ⊕ admin JSON_

where guest and admin JSON are as follows:

```
guest JSON: {"username":"guest","date":"blablabla"}
admin JSON: {"username":"administrator"}
```

Note that we can easily predict part of the date value (we know it starts with today’s date), but in this case, we don’t really care, since our target plaintext (admin JSON) does not overlap with it.

A small Python script can help perform the operation:

```
#!/usr/bin/python

if __name__ == '__main__':
  cipherText = '82532b2136348aaa1fa7dd2243da1cc9fb13037c49259e5ed70768d4e9baa1c90b96f9e8bfa12882f878bb7fc49f0953b14348637bec'

  guestJSON = '{"username":"guest","date":"blablabla"}'
  adminJSON = '{"username":"administrator"}'

  cBytes = bytearray(cipherText.decode('hex'))
  gBytes = bytearray(guestJSON)
  aBytes = bytearray(adminJSON)

  print ''.join(format(c^g^a, '02x') for c,g,a in zip(cBytes,gBytes,aBytes))
```

Run the script to get the new cookie value:

```
$ python flip.py

82532b2136348aaa1fa7dd2243dc0dc1e10948231f339e5edd57708b
```

Note that the encrypted value is much shorter than before, but that’s OK – our target plaintext was shorter too!

Let’s make sure this value works by modifying AUTH cookie in the browser:

![screenshot](/assets/2017-06-21-stream-ciphers-and-message-integrity/06-admin-cookie-new.jpg)

And make sure we have administrative privileges on the server (i.e. access to the Edit function):

![screenshot](/assets/2017-06-21-stream-ciphers-and-message-integrity/07-edit-new.jpg)

Congratulations, we have just successfully spoofed an administrator’s cookie by modifying the ciphertext **without** knowing the key!


## What’s inside?

Okay, since we know the key anyway, let’s go ahead and verify what’s inside the modified cookie:

```
$ echo 82532b2136348aaa1fa7dd2243dc0dc1e10948231f339e5edd57708b | xxd -r -p | openssl rc4 -K "6117a495bf3dd7cd2e0d8bcb9f79e1dc"

{"username":"administrator"}
```

Exactly as expected!


## Conculusion

Cryptography is an amazing world, full of beautiful and mind-blowing things. But it’s also a world full of danger despite modern cryptographic algorithms very rarely having issues in themselves. Occasionally, we learn about something huge that makes us phase out a weak algorithm, but that is not our everyday worry. What we need to watch out for is usage of these wonderful, robust and secure algorithms in each particular case – in applications, in libraries, in operating systems, in hardware. It is extremely important to implement and to use cryptography right! The [WEP fiasco](https://en.wikipedia.org/wiki/Wired_Equivalent_Privacy#Security_details) is a great example of cryptography done wrong.

In addition to secrecy, integrity is extremely important. When I lock something up in a safe and come back to retrieve it later, I expect to find the exact same item I put there before. Nobody should be able to mess with my stuff inside the safe! Very often in the commercial world, integrity is more important than secrecy, as it creates the very basis for all trust relationships.

To protect the integrity of the encrypted data both in transit and at rest, one should use a [message authentication code](https://en.wikipedia.org/wiki/Message_authentication_code). MACs use a combination of digital signature and encryption to ensure message integrity. A correctly implemented decryption function will verify MAC and will reject the message if the codes do not match. Newer algorithms such as AES-GCM or AES-CCM include MACs built into the implementation and should be used whenever possible.

Finally, remember that SANS did this on purpose – it’s a hacking game and a learning tool! In this case, the more ways there are to hack something the better! Your application is presumably different – don’t be an example of incorrectly used stream ciphers or bad coding practices!


## Links

1. [SANS Holiday Hack Challenges](https://holidayhackchallenge.com/)
1. [Stream cipher](https://en.wikipedia.org/wiki/Stream_cipher)
1. [Stream cipher attack](https://en.wikipedia.org/wiki/Stream_cipher_attack)
1. [Bit-flipping attack](https://en.wikipedia.org/wiki/Bit-flipping_attack)
1. [RC4](https://en.wikipedia.org/wiki/RC4)
1. [The Magic of XOR](https://www.cs.umd.edu/class/sum2003/cmsc311/Notes/BitOp/xor.html)
1. [Applied Cryptography](https://www.schneier.com/books/applied_cryptography/)
1. [How to choose an Authenticated Encryption Mode](https://blog.cryptographyengineering.com/2012/05/19/how-to-choose-authenticated-encryption/)
