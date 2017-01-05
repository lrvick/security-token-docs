# Advanced Setup

These steps will help generate a new PGP keychain for use with a Security Token
with an offline backup. This backup will allow you to create duplicate keys
if you desire, or recover in the case of a lost/damaged key.

You should ideally perform these steps from an "airgapped" system, such as from
a Linux Live-CD not connected to the internet.

Unlike the "Simple Setup" steps, this will result in your Master key being
offline and only Subkeys will exist on the Security Token. This will mean your
Security Token will lack the Certify permission, meaning it will not be able to
sign other GPG keychains.

All other normal GPG operations will work as expected and in general you will
not need your Master key except to add/revoke subkeys, and sign the identities
of other people.

## Prepare Offline Storage Directory

This directory needs to be kept on an offline device (USB drive, etc) in a
stable location (Fireproof box, safe, under your matress, etc)

This will contain:
 * Backup plaintext secret keys, useful for emergencies
 * Revocation certificate
 * Master key, used to sign other keys

Insert the USB drive and observe the path it mounts to.

Here we will assume /Media/UsbStick

In the terminal, create a new directory on this device:

```
$ mkdir /Media/UsbStick/pgp
```

## Generate a Master Key

This important key will be used to sign other keys and for generation of the
subkeys that are needed for the Security Token.  This tutorial will start by
generating a new key in "Expert mode" to allow the needed customization.

```
$ gpg --full-gen-key --expert
```

The first prompt will ask for the type of key desired.  This is an RSA sign-only key: 

```
gpg (GnuPG) 2.0.27; Copyright (C) 2015 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

gpg: keyring `/Users/some.person/.gnupg/secring.gpg' created
gpg: keyring `/Users/some.person/.gnupg/pubring.gpg' created

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
Your selection? 4
```

Enter 4096 for key size. There are few good reasons to have a key size smaller
than 4096bit given ample available CPU today.

```
RSA keys may be between 1024 and 4096 bits long.

What keysize do you want? (4096) 4096
Requested keysize is 4096 bits
```

It's a good practice to make the keys expire at some point. If you happen to
lose access to your keychain, you at least know when you create a new set
the doppelganger identity will in time expire and no longer require
explanation.

One year (1y) is used here.  This expiration can be extended in the future to
a new date.

```
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years

Key is valid for? (0) 1y

Key expires at Sat May 28 15:08:05 2016 PDT

Is this correct? (y/N) y
```

Now, enter your personal info. 

This is for the primary user id (later, secondary user ids can be added): 

```
GnuPG needs to construct a user ID to identify your key.

Real name: Some Person
Email address: some.person@company.com
Comment:                        
You selected this USER-ID:
    "Some Person <some.person@company.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
```

Finally, enter a strong password (12+ character length, including lower/upper
case letters, numbers, and special characters) and the master key generation
will be complete.  This password will be used for signing other keys,
revocation, and further manipulation of your key (and its subkeys). 

When the Security Token is set up you likely won't need this password for daily
operation but it should be maintained in your brain. You could also store only
part of the password in memory, and a long complex part in a usb drive in a 
secure location.

```
You need a Passphrase to protect your secret key.    

We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

gpg: /Users/some.person/.gnupg/trustdb.gpg: trustdb created
gpg: key B44C2E4A marked as ultimately trusted
public and secret key created and signed.

gpg: checking the trustdb
gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
gpg: next trustdb check due at 2016-05-28
pub   4096R/B44C2E4A 2015-05-29 [expires: 2016-05-28]
      Key fingerprint = 9113 59D3 992F 6A85 2BD6  C0ED 9559 7471 B44C 2E4A
uid       [ultimate] Some Person <some.person@gmail.com>

Note that this key cannot be used for encryption.  You may want to use
the command "--edit-key" to generate a subkey for this purpose.
 
gpg> save
```

Take note of the key id (taken from above) as you will need it to further
manipulate your key (adding user ids, adding subkeys, etc):

```
pub   4096R/B44C2E4A 2015-05-29 [expires: 2016-05-28]
```

The key id in this example is B44C2E4A. 

Alternatively, you can retrieve it with a gpg command

```
# gpg --list-keys
/Users/some.person/.gnupg/pubring.gpg
--------------------------------
pub   4096R/B44C2E4A 2015-05-29 [expires: 2016-05-28]
uid       [ultimate] Some Person <some.person@company.com>
```

## Add User IDs (email aliases) to an Existing Key

Add any other relevant user ids using the adduid command in the GPG console:

```
# gpg --edit-key B44C2E4A
gpg (GnuPG) 2.0.27; Copyright (C) 2015 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

pub  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28  usage: SC  
                     trust: ultimate      validity: ultimate
[ultimate] (1). Some Person <some.person@company.com>

gpg> adduid
Real name: Some Person
Email address: some.person@gmail.com
Comment:                       
You selected this USER-ID:
    "Some Person <some.person@gmail.com>"
Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O

You need a passphrase to unlock the secret key for
user: "Some Person <some.person@company.com>"
4096-bit RSA key, ID B44C2E4A, created 2015-05-29

pub  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28  usage: SC  
                     trust: ultimate      validity: ultimate
[ultimate] (1)  Some Person <some.person@company.com>
[ unknown] (2). Some Person <some.person@gmail.com>
```

The primary user is indicated with a period: ".".  You can see above that the
newly created user id now is the primary user. You can change the primary user
back to Some User's "@company.com" uid by first selecting it:

```
gpg> uid 1

pub  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28  usage: SC  
                     trust: ultimate      validity: ultimate
[ultimate] (1)* Some Person <some.person@company.com>
[ unknown] (2). Some Person <some.person@gmail.com>
A selected user is indicated with an asterisk: "*".  The uid command toggles users into a "selected" state and can select multiple users, so be careful.  Now set the selected user to "primary" and unselect the user to show the primary state: 
gpg> primary

pub  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28  usage: SC  
                     trust: ultimate      validity: ultimate
[ultimate] (1)* Some Person <some.person@company.com>
[ unknown] (2)  Some Person <some.person@gmail.com>
 
gpg> uid 1
 
pub  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28  usage: SC  
                     trust: ultimate      validity: ultimate
[ultimate] (1). Some Person <some.person@company.com>
[ unknown] (2)  Some Person <some.person@gmail.com>
 
gpg> save
```

## Generate Revocation Certificate

If your keys are compromised for any reason you will need a revocation
certificate to revoke (untrust) your keys.  Generate the revocation certificate
and save it in the safe place created earlier:

```
$ gpg --output /Media/UsbStick/pgp/revocation-certificate.txt --gen-revoke B44C2E4A

sec  4096R/B44C2E4A 2015-05-29 Some Person <some.person@company.com>

Create a revocation certificate for this key? (y/N) y
Please select the reason for the revocation:         
  0 = No reason specified
  1 = Key has been compromised
  2 = Key is superseded
  3 = Key is no longer used
  Q = Cancel
(Probably you want to select 1 here)
Your decision? 1
Enter an optional description; end it with an empty line:
> 
Reason for revocation: Key has been compromised
(No description given)
Is this okay? (y/N) y

You need a passphrase to unlock the secret key for
user: "Some Person <some.person@company.com>"
4096-bit RSA key, ID B44C2E4A, created 2015-05-29

ASCII armored output forced.
Revocation certificate created.
```
Please move it to the backup medium created earlier which you can hide away;
if a bad actor gets access to this certificate he can use it to make your key
unusable, and potentially manipulate people into trusting a new fake key he 
created for you instead.

It is not a bad idea to print this certificate and store it away, just in case
your media becomes unreadable.  But have some caution:  The print system of
your machine might store the data and make it available to others!


## Add Subkeys to an Existing Key

Three subkeys are needed for full GPG support of a Security Token:
 * Signing key
 * Encryption key
 * Authentication key

These subkeys will be removed from the keypair and placed onto the Security
Token. 

The following assume that the above sections have been completed, and continues
the examples in those sections. 

To start, edit the key in "Expert mode" so that there is more control in the
key creation process:

```
$ gpg --edit-key --expert B44C2E4A

gpg (GnuPG) 2.0.27; Copyright (C) 2015 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

pub  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28  usage: SC  
                     trust: ultimate      validity: ultimate
[ultimate] (1). Some Person <some.person@company.com>
[ultimate] (2)  Some Person <some.person@gmail.com>
```

Generating subkeys is similar to the initial master key generation, so the
walkthroughs purposefully aren't as verbose.

When finished adding subkeys, make sure to save and backup! Adding the keys to
the Security Token is a destructive process, so you'll be archiving the backup
in a safe place and also use it to fix the Security Token if you mess up
somewhere.

### Signing Subkey

```
gpg> addkey
Key is protected.

You need a passphrase to unlock the secret key for
user: "Some Person <some.person@company.com>"
4096-bit RSA key, ID B44C2E4A, created 2015-05-29

Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
Your selection? 4

RSA keys may be between 1024 and 4096 bits long.

What keysize do you want? (4096) 4096

Requested keysize is 4096 bits

Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years

Key is valid for? (0) 1y

Key expires at Tue May 31 09:35:55 2016 PDT
Is this correct? (y/N) y

Really create? (y/N) y  

We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

pub  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28  usage: SC  
                     trust: ultimate      validity: ultimate
sub  4096R/458785FA  created: 2015-06-01  expires: 2016-05-31  usage: S   
[ultimate] (1). Some Person <some.person@company.com>
[ultimate] (2)  Some Person <some.person@gmail.com>
 
gpg> save
```

### Encryption Subkey

```
gpg> addkey
Key is protected.

You need a passphrase to unlock the secret key for
user: "Some Person <some.person@company.com>"
4096-bit RSA key, ID B44C2E4A, created 2015-05-29

Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
Your selection? 6
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (4096) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years

Key is valid for? (0) 1y

Key expires at Tue May 31 09:40:00 2016 PDT
Is this correct? (y/N) y

Really create? (y/N) y  

We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

pub  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28  usage: SC  
                     trust: ultimate      validity: ultimate
sub  4096R/458785FA  created: 2015-06-01  expires: 2016-05-31  usage: S   
sub  4096R/25238CF1  created: 2015-06-01  expires: 2016-05-31  usage: E   
[ultimate] (1). Some Person <some.person@company.com>
[ultimate] (2)  Some Person <some.person@gmail.com>
 
gpg> save
```

### Authentication Subkey

```
gpg> addkey
Key is protected.

You need a passphrase to unlock the secret key for
user: "Some Person <some.person@company.com>"
4096-bit RSA key, ID B44C2E4A, created 2015-05-29

Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only)
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
Your selection? 8

Possible actions for a RSA key: Sign Encrypt Authenticate 
Current allowed actions: Sign Encrypt 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? s

Possible actions for a RSA key: Sign Encrypt Authenticate 
Current allowed actions: Encrypt 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? e

Possible actions for a RSA key: Sign Encrypt Authenticate 
Current allowed actions: 


   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? a

Possible actions for a RSA key: Sign Encrypt Authenticate 
Current allowed actions: Authenticate 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection? q

RSA keys may be between 1024 and 4096 bits long.

What keysize do you want? (4096) 4096

Requested keysize is 4096 bits    

Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years

Key is valid for? (0) 1y

Key expires at Tue May 31 09:43:00 2016 PDT
Is this correct? (y/N) y

Really create? (y/N) y  

We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

pub  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28  usage: SC  
                     trust: ultimate      validity: ultimate
sub  4096R/458785FA  created: 2015-06-01  expires: 2016-05-31  usage: S  
sub  4096R/25238CF1  created: 2015-06-01  expires: 2016-05-31  usage: E   
sub  4096R/156CA250  created: 2015-06-01  expires: 2016-05-31  usage: A   
[ultimate] (1). Some Person <some.person@company.com>
[ultimate] (2)  Some Person <some.person@gmail.com>
 
gpg> save
```

## Backup Keys

Now backup your keys to your previously created offline storage directory:

```
$ gpg -a --export-secret-keys B44C2E4A > /Media/UsbStick/pgp/B44C2E4A.master.asc
$ gpg -a --export-secret-subkeys B44C2E4A > /Media/UsbStick/pgp/B44C2E4A.subkeys.asc
$ gpg -a --export B44C2E4A > /Media/UsbStick/pgp/B44C2E4A.public.asc
```
 
## Write Subkeys to Security Token
 
This is a destructive operation as it will leave only non-secret "stubs" in
your current .gnupg directory.

```
gpg --edit-key B44C2E4A
gpg (GnuPG) 2.0.27; Copyright (C) 2015 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

pub  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28  usage: SC  
                     trust: ultimate      validity: ultimate
sub  4096R/458785FA  created: 2015-06-01  expires: 2016-05-31  usage: S   
sub  4096R/25238CF1  created: 2015-06-01  expires: 2016-05-31  usage: E   
sub  4096R/156CA250  created: 2015-06-01  expires: 2016-05-31  usage: A   
[ultimate] (1). Some Person <some.person@company.com>
[ultimate] (2)  Some Person <some.person@gmail.com>

gpg> toggle

sec  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28
ssb  4096R/458785FA  created: 2015-06-01  expires: never     
ssb  4096R/25238CF1  created: 2015-06-01  expires: never     
ssb  4096R/156CA250  created: 2015-06-01  expires: never     
(1)  Some Person <some.person@company.com>
(2)  Some Person <some.person@gmail.com>

gpg> key 1

sec  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28
ssb* 4096R/458785FA  created: 2015-06-01  expires: never     
ssb  4096R/25238CF1  created: 2015-06-01  expires: never     
ssb  4096R/156CA250  created: 2015-06-01  expires: never     
(1)  Some Person <some.person@company.com>
(2)  Some Person <some.person@gmail.com>

gpg> keytocard

Signature key ....: [none]
Encryption key....: [none]
Authentication key: [none]

Please select where to store the key:
   (1) Signature key
   (3) Authentication key
Your selection? 1

You need a passphrase to unlock the secret key for
user: "Some Person <some.person@company.com>"
4096-bit RSA key, ID 458785FA, created 2015-06-01

sec  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28
ssb* 4096R/458785FA  created: 2015-06-01  expires: never     
                     card-no: 0006 03509605
ssb  4096R/25238CF1  created: 2015-06-01  expires: never     
ssb  4096R/156CA250  created: 2015-06-01  expires: never     
(1)  Some Person <some.person@company.com>
(2)  Some Person <some.person@gmail.com>

gpg> key 1

sec  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28
ssb  4096R/458785FA  created: 2015-06-01  expires: never     
                     card-no: 0006 03509605
ssb  4096R/25238CF1  created: 2015-06-01  expires: never     
ssb  4096R/156CA250  created: 2015-06-01  expires: never     
(1)  Some Person <some.person@company.com>
(2)  Some Person <some.person@gmail.com>

gpg> key 2

sec  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28
ssb  4096R/458785FA  created: 2015-06-01  expires: never     
                     card-no: 0006 03509605
ssb* 4096R/25238CF1  created: 2015-06-01  expires: never     
ssb  4096R/156CA250  created: 2015-06-01  expires: never     
(1)  Some Person <some.person@company.com>
(2)  Some Person <some.person@gmail.com>

gpg> keytocard       

Signature key ....: 5074 52CA B453 6E2F 4625  C248 4124 0022 4587 85FA
Encryption key....: [none]
Authentication key: [none]

Please select where to store the key:
   (2) Encryption key
Your selection? 2

You need a passphrase to unlock the secret key for
user: "Some Person <some.person@company.com>"
4096-bit RSA key, ID 25238CF1, created 2015-06-01

sec  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28
ssb  4096R/458785FA  created: 2015-06-01  expires: never     
                     card-no: 0006 03509605
ssb* 4096R/25238CF1  created: 2015-06-01  expires: never     
                     card-no: 0006 03509605
ssb  4096R/156CA250  created: 2015-06-01  expires: never     
(1)  Some Person <some.person@company.com>
(2)  Some Person <some.person@gmail.com>

gpg> key 2

sec  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28
ssb  4096R/458785FA  created: 2015-06-01  expires: never     
                     card-no: 0006 03509605
ssb  4096R/25238CF1  created: 2015-06-01  expires: never     
                     card-no: 0006 03509605
ssb  4096R/156CA250  created: 2015-06-01  expires: never     
(1)  Some Person <some.person@company.com>
(2)  Some Person <some.person@gmail.com>

gpg> key 3

sec  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28
ssb  4096R/458785FA  created: 2015-06-01  expires: never     
                     card-no: 0006 03509605
ssb  4096R/25238CF1  created: 2015-06-01  expires: never     
                     card-no: 0006 03509605
ssb* 4096R/156CA250  created: 2015-06-01  expires: never     
(1)  Some Person <some.person@company.com>
(2)  Some Person <some.person@gmail.com>

gpg> keytocard
Signature key ....: 5074 52CA B453 6E2F 4625  C248 4124 0022 4587 85FA
Encryption key....: A043 823F A59D 181C 3419  092D F71C CC8E 2523 8CF1
Authentication key: [none]

Please select where to store the key:
   (3) Authentication key
Your selection? 3

You need a passphrase to unlock the secret key for
user: "Some Person <some.person@company.com>"
4096-bit RSA key, ID 156CA250, created 2015-06-01

sec  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28
ssb  4096R/458785FA  created: 2015-06-01  expires: never     
                     card-no: 0006 03509605
ssb  4096R/25238CF1  created: 2015-06-01  expires: never     
                     card-no: 0006 03509605
ssb* 4096R/156CA250  created: 2015-06-01  expires: never     
                     card-no: 0006 03509605
(1)  Some Person <some.person@company.com>
(2)  Some Person <some.person@gmail.com>

gpg> key 3

sec  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28
ssb  4096R/458785FA  created: 2015-06-01  expires: never     
                     card-no: 0006 03509605
ssb  4096R/25238CF1  created: 2015-06-01  expires: never     
                     card-no: 0006 03509605
ssb  4096R/156CA250  created: 2015-06-01  expires: never     
                     card-no: 0006 03509605
(1)  Some Person <some.person@company.com>
(2)  Some Person <some.person@gmail.com>

gpg> save
```

## Cleanup

The following is critical to ensure no private keys are left on disk, and that
you are in fact are only relying on the private keys in your Security Token.

If you heeded the advice in this document of working from a LiveCD this still
serves as a good test.

We opt for a "clean room" approach here to ensure you could use your key as
expected on a new system.

```
$ rm .gnupg -rf
$ killall gpg-agent
$ gpg --import /Media/UsbStick/pgp/B44C2E4A.public.asc
$ gpg --card-status
$ gpg --edit-key B44C2E4A
 
gpg (GnuPG) 2.0.27; Copyright (C) 2015 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

pub  4096R/B44C2E4A  created: 2015-05-29  expires: 2016-05-28  usage: SC  
                     trust: unknown      validity: unknown
sub  4096R/458785FA  created: 2015-06-01  expires: 2016-05-31  usage: S   
sub  4096R/25238CF1  created: 2015-06-01  expires: 2016-05-31  usage: E   
sub  4096R/156CA250  created: 2015-06-01  expires: 2016-05-31  usage: A   
[unknown] (1). Some Person <some.person@company.com>
[unknown] (2)  Some Person <some.person@gmail.com>

gpg> trust

Please decide how far you trust this user to correctly
verify other users' keys (by looking at passports,
checking fingerprints from different sources...)?

 1 = Don't know
 2 = I do NOT trust
 3 = I trust marginally
 4 = I trust fully
 s = please show me more information
 m = back to the main menu

Your decision? 5
 
gpg> quit
```
