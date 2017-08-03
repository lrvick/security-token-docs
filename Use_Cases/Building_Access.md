# Building Access

Most building access systems tend to use 125KHz unencrypted HID cards such as a
Proxcard II.

These can be cloned trivially to any writable T5577 tag via a professional RFID
analysis tool like a Proxmark 3 or even a cheap generic "HID cloner" off Ebay.

Newer HID cards are based on the Desfire EV1 which are theoretically open to
side channel attacks that would take several hours but no specific attacks have
been published so far.

Other buildings often support 13.56MHz MiFare NFC cards with no encryption. A
smartphone can easily clone these to most NFC tags, or a NFC enabled Security
Token like a YubiKey Neo.
