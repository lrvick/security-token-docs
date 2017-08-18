# Full Disk Encryption

Viable solutions exist for Yubikey-based FDE on OSX/Windows that require either
a very customized operating system install, or custom reader.

## Linux

FDE + Yubikey is going to be fairly straight forward for Linux/(Free/Open)BSD
via HMAC-SHA1 or pin+GPG.

TODO: Add in details and links and don't make people google it all

## Windows/OSX

In general Windows/OSX are simply not a great option if a high level of
security is desired. Being proprietary they can not be easily modified to
meet custom requirements.

That said a couple possible paths exist to get some level of security token
integration.

### VT-d Virtual Machine

There are currently not any known paths to do FDE and then -directly- bootstrap
unmodified OSX/Windows kernels which will expect to talk directly to real disk
partition. That said, on modern hardware with VT-d support you could pass
through a lightweight hypervisor. Then you get to use OSX/Windows in a jailed
environment on the other side of an IOMMU where they can't access the memory
that holds the passphrase or directly interact with the real disk.

The flow for Windows/OSX would be, boot to tiny bootstrapped linux system
(< ~100MB Mem overhead) that maintains FDE then chain-loads windows/OSX into a
hypervisor. While a pain to set up initially, this is totally possible on VT-d
apable hardware with minimal performance penalty.

A script to bootstrap a Windows qemu guest with VT-d is here for reference:
https://github.com/lrvick/dotfiles/blob/master/.local/bin/win

### Custom HID reader

If you simply trust OSX/Windows fully or your threat model does not warrant any
of the above, you could just use straight up HID via a custom reader.

A simple prototype would entail an Adafruit PN532 NFC Shield + Arduino Uno with
a very basic firmware that provides a pre-coded challenge to the NFC hardware
token, and then captures a long reply string from the token which it proxies
over USB/HID to type in the passphrase as though it was a simply a USB Keyboard.

This is far from ideal or secure but if you were to built a reader that itself
uses a secure enclave of some type to protect the challenge you might find a
solution that is suitable for use in a very specific environments.

