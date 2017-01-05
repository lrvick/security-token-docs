# GPG - Simple Setup

These steps are the simplest way to initialize a Security Token for GPG however If you lose the key or forget the PIN, you will not be able to recover it!

This is only recommended where you universally have administrators or peers capable of revoking and rotating all of your credentials if nessesary.

If you are wanting to setup a GPG on a Security Token for mixed or personal use, or are an administator it is recommended to use the Advanced Setup which will allow you an opprotunity to make a secure offline backup of key data.

## Configure PIN codes

Save the pins in your security-token encrypted password manager.

Do not use existing pins ...

```
$ gpg2 --card-edit
Application ID ...: D2760001240102000006037030240000
Version ..........: 2.0
Manufacturer .....: Yubico
Serial number ....: 03703024
Name of cardholder: [not set]
Language prefs ...: [not set]
Sex ..............: unspecified
URL of public key : [not set]
Login data .......: [not set]
Signature PIN ....: forced
Key attributes ...: 4096R 4096R 4096R
Max. PIN lengths .: 127 127 127
PIN retry counter : 3 3 3
Signature counter : 0
Signature key ....: [none]
Encryption key....: [none]
Authentication key: [none]
General key info..: [none]

gpg/card> admin
Admin commands are allowed

gpg/card> passwd
gpg: OpenPGP card no. D2760001240102000006037030240000 detected

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? 1
(You will have to type the old PIN (123456) and enter a new pin.
PIN changed.

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? 3
(You will have to type the old Admin PIN (12345678) and enter a new admin pin.
PIN changed.

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? q
```

## Set Personal Details

This is all optional but useful if someone else finds your key to be able to return it to you.

```
$ gpg2 --card-edit
Application ID ...: D2760001240102000006037030240000
Version ..........: 2.0
Manufacturer .....: Yubico
Serial number ....: 03703024
Name of cardholder: [not set]
Language prefs ...: [not set]
Sex ..............: unspecified
URL of public key : [not set]
Login data .......: [not set]
Signature PIN ....: forced
Key attributes ...: 4096R 4096R 4096R
Max. PIN lengths .: 127 127 127
PIN retry counter : 3 3 3
Signature counter : 0
Signature key ....: [none]
Encryption key....: [none]
Authentication key: [none]
General key info..: [none]

gpg/card> admin
Admin commands are allowed

gpg/card> name
Cardholder's surname: Person
Cardholder's given name: Some

gpg/card> sex
Sex ((M)ale, (F)emale or space): M

gpg/card> login
Login data (account name): sperson

gpg/card> lang
Language preferences: en

gpg/card> quit
```

## Generate Keychain

This command will force your card to generate GPG Subkeys for Signing, Encryption, and Authentication respectively and store them in the card. Your computer will never see the private key as the entire process is self-contained inside the card.

```
$ gpg2 --card-edit
Application ID ...: D2760001240102000006037030240000
Version ..........: 2.0
Manufacturer .....: Yubico
Serial number ....: 03703024
Name of cardholder: Some Person
Language prefs ...: en
Sex ..............: male
URL of public key : [not set]
Login data .......: sperson
Signature PIN ....: forced
Key attributes ...: 4096R 4096R 4096R
Max. PIN lengths .: 127 127 127
PIN retry counter : 3 3 3
Signature counter : 0
Signature key ....: [none]
Encryption key....: [none]
Authentication key: [none]
General key info..: [none]

gpg/card> admin
Admin commands are allowed

gpg/card> generate
Make off-card backup of encryption key? (Y/n) n
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 2y
Key expires at Wed Dec 14 15:55:04 2018 PST
Is this correct? (y/N) y

GnuPG needs to construct a user ID to identify your key.

Real name: Some Person
Email address: person@company.com
Comment:
You selected this USER-ID:
    "Some Person <person@company.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
(Note that this step will take a little while ~1 minute)
gpg: key ABAEE282 marked as ultimately trusted
public and secret key created and signed.

gpg: checking the trustdb
gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
gpg: depth: 0  valid:   4  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 4u
gpg: next trustdb check due at 2018-08-19
pub   4096R/ABAEE282 2016-02-11 [expires: 2018-12-14]
      Key fingerprint = 5D97 0241 09AA 2A20 EC1F  AAEF 2223 BDB8 ABAE E282
uid       [ultimate] Some Person <person@company.com>
sub   4096R/C63542DE 2016-02-11 [expires: 2018-12-14]
sub   4096R/051D07BD 2016-02-11 [expires: 2018-12-14]

gpg/card> quit
```
