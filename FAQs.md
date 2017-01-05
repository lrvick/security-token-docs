# Frequently Asked Questions

## Why not a smartphone app?

Smartphone apps like Google Authenticator are certainly better than nothing at all, and great to protect personal accounts. In a larger organization however an attacker will simply find a "low-hanging-fruit" user that has a vulnerable smartphone, and steal codes from that at will.

## Why not SMS?

Most cell phones will blindly connect to anything that claims to be a compatible cell phone tower, and will use any encryption methods that "tower" says it supports. This means, in practice, that most SMS can be intercepted by anyone. In many cases it only takes $20 in hardware and 2TB of disk space.

Even easier methods to intercept SMS may exist if you forward messages through email, Google Voice, Google Hangouts, or a wearable device such as a Fitbit or Pebble.

Many services offer to allow SMS as a "backup" method for a hardware token. This entirely defeats the point, and such advice should be ignored.

If you would like to intercept your own SMS messages, see: [http://www.rtl-sdr.com/receiving-decoding-decrypting-gsm-signals-rtl-sdr/]

## What if I lose it?

Obviously you should try really hard not to do this, but life happens. Most personal services allow you to print out a set of "backup codes" to put aside as an "escape hatch". With corporate services you would simply contact a network administrator to have your existing token revoked and a new one issued.

## What if someone steals it?

Most security tokens default to a "locked" state and require a pin code to "unlock" them until they are unplugged or manually locked again (perhaps when you lock your computer). Much like what would happen if someone stole your ATM card, an attacker that fails to enter the correct pin code 3 times, will disable the device.

## What if it gets damaged?

In most cases it is possible to keep secure offline backups of the contents of a security token at the time data is added to it. This requires considerable additional knowledge of good cryptography practices and extra work at setup time, but it is the best way to ensure a reliable and replaceable digital "keychain" you can use for years to come.

It is typically not advised to keep backups of security key data around unless it is a "master" key such as ones held by account owners or system administrators that understand how to properly secure said backups.

In general just refer to "What if I lose it?"
