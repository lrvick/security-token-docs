# Password Management

There are a lot of password management solutions so here we will focus on the
most common.

Here we are specifically hilighting high level security/usability attributes of
each, and specifically which have support for things like hardware token
encryption/signing/2FA, and those that do not.

Name	         | OSS | Sig.| WoT | HSM | 2FA | Team | iOS | And.| OSX | Win | Lin.| Cost
:--------------|:---:|:---:|:---:|:---:|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:----:
Password Store |  X  |  X	 |  X  |  X	 |  X  |  X	  |  X  |  X  |  X  |  X  |  X	| Free
Encryptr	     |  X  |  	 |     |     |     |      |  X  |  X  |  X  |  X  |  X  | Free
KeePass	       |  X  |  	 |     |     |  X  |  X	  |  X	|  X  |  X  |  X  |  X	| Free
KeePassX       |  X  |  	 |     |     |     |  X	  |     |     |  X  |  X  |  X	| Free
KeePassXC      |  X  |  	 |     |     |  X  |  X	  |     |     |  X  |  X  |  X	| Free
Password Safe	 |  X  |  	 |     |     |  X  |      |  X	|     |     |  X  |  X	| Free
Password BOX   |     |  	 |     |     |  	 |      |  X	|  X  |  X  |  X  |     | Free
Pwsafe	 	     |     |  	 |     |     |  X  |      |  X	|     |  X  |	    |     | Free
Dashlane	 	   |     |  	 |     |     |     |  X   |  X  |  X  |  X  |  X  |   	| Free
Passpack	 	   |     |  	 |     |     |  X  |  X   |  X  |  X  |  X  |  X  |   	| $48/y
1Password	 	   |     |  	 |     |     |     |  X	  |  X	|  X  |  X  |  X  |     | $50
Keeper	 	   |	   |   	 |     |     |     |  X	  |  X	|  X  |  X  |  X  |  X	| $30/y
LastPass	 	   |	   |   	 |     |     |     |  X	  |  X	|  X  |  X  |  X  |  X	| $12/y

## What should I use?

The password manager that checks the most security and compatibility boxes
above is Password Store, so the instructions below will focus on that. If you
don't maintain highly sensitive credentials perhaps the tradeoffs of another
tool will work better for you. Any password manager is better than trusting
memory, a text file on your machine, or post-it-notes.

Pass will by no means suit all use cases, as there is no one-size-fits-all hero
in password management tools right now. You are encouraged to do your own
research.

It is worth noting that while Pass may check the most boxes, it may be one of
the most difficult to set up initially and you will have to factor the
technical capability of yourself or users you support into consideration.

With those disclaimers out of the way, the reason Password Store supports
everything is that both the CLI tools and GUIs like Android Password Store and
QTPass all share a very simple common understanding of how passwords should be
encrypted, stored and interacted with under the hood.

A Password Store repository is just GPG encrypted text files in a folder.
Optionally "pass" tools all support that folder being managed via Git, and can
wrap git commands and sync for you. This allows for a very simple and easy to
reason about password management strategy, that does not require any
specialized tools except for convenience.

If you GPG encrypt a text file and put it in a folder, any "Pass" compatible
program will consider that folder to be a valid password store.

The source code of the reference CLI implementation of pass is nothing but
a simple bash script that wraps gpg and git commands making it easy to audit
and trust. You can however always manually decrypt pass with just GPG as you
like to integrate with any tooling you need.

There is always a new flavor of the week when it comes to password managers
however gpg makes for a very simple and predictable backend that has been
maintained and audited by governments and the open source community since 1999.

## Pass Setup

#### Required:

* A hardware token with GPG
* Git configured to sign all commits

#### Steps:

1. Set up a Git remote you can access from anywhere

    Github example via 'hub':
    ```
    $ hub create -p passdb
    ```

2. Obtain your long-form GPG Public Key ID:

    ```
    $ gpg --list-keys --with-colons

    tru::1:1481584702:1510091812:3:1:5
    pub:u:4096:1:E90A401336C8AAA9:1241834622:1510091812::u:::scESCA:::::::
    fpr:::::::::6B61ECD76088748C70590D55E90A401336C8AAA9:
    uid:u::::1447019812::1001C6683FA88F0D1AE082F4C55F84C687FD2732:: John H Doe <john.doe@company.com>:
    ...
    ```

3. Initialize Password Store with your GPG ID and Git Remote

    ```
    pass init "6B61ECD76088748C70590D55E90A401336C8AAA9"
    pass git init
    pass git remote add origin "git@github.com:your-user/passdb.git"
    ```

4. Set your first password

    This will generate a random 50 character password, gpg encrypt it to your
    key, and save it as ~/.password-store/Personal/gmail.gpg

    It will then make a commit on your behalf to the repo defined at
    ~/.password-store/.

    Your Security Token should ideally begin blinking so you can touch an approve
    it signing the commit for you.

    ```
    pass generate Personal/gmail 50
    ```

5. Sync to Git Remote

    ```
    pass git push
    ```

### Pass Integrations

#### QTPass

Graphical user interface for pass that supports Linux, BSD, OSX, and Windows

Website:  https://qtpass.org/

#### Passmenu

Very simple dmenu based GUI for pass on Linux and BSD that supports keyboard
emulation via xdotool to auto-type passwords on demand.

This allows a sweet spot of ease of use without relying on the system
clipboard or complex browser plugins that introduce additional attack surface.

Ships with "pass" package on most unix/linux distributions.

#### Android Password Store

Provides a GUI for Android that can add/remove/modify/sync passwords against
your remote Git repo.

Supports NFC or USB OTG Security Tokens via OpenKeychain.

OpenKeychain: https://f-droid.org/packages/org.sufficientlysecure.keychain/  
Android Password Store: https://f-droid.org/packages/com.zeapo.pwdstore/

#### Passforios

Provides a GUI for iOS that can add/remove/modify/sync passwords against
your remote Git repo.

Website: https://mssun.github.io/passforios/
