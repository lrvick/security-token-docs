Email is very easy to spoof. Anyone can trivially send email from anyone else in small quantities without tripping off anti-spam features in most email services.

Tools like DMARC help with this by proving email came from a given domain, however even if all that is implemented properly in an orginization, any user can still pretend to be any user within that orginization. A compromised account from sales for instance could be used to send mail as a CEO or Head of IT.

Using GPG and Security Tokens we have the ability to cryptographically sign mail in such a way that we can prove it came from someone physically in posession of a given users hardware. Supported email clients will often show such mail in "green" not unlike the "green lock" you see in a status bar.

In general you should always assume the possibility all email you receive is fradulent if it is not pesonally signed, or at the very least signed via DMARC at the domain level.

Most mail is not personally GPG signed or encrypted yet, but GPG is still highly useful for small groups of people to be able to correspond with each other securely transmit even highly sensative data such as passwords or database dumps.

Use a supported Email client/Plugin:

Thunderbird
Enigmail
K-9 Mail
OpenKeychain
Claws Mail
The GPG Plugin
Mutt
Built-in!
Mail.app
GPGMail from GPG Suite
Note: Don't install any of the other GPG Suite tools as they are very out of date
Gmail
Goopg Chrome Extension
Outlook
GpgOL

