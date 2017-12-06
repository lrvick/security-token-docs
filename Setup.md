# Setup

## Install required software

### OSX

GUI Only: https://developers.yubico.com/yubioath-desktop/Releases/

GUI + CLI Tools:
```
$ brew tap homebrew/versions
$ brew install homebrew/versions/gnupg21 cartr/qt4/qt ykpers pinentry pinentry-mac swig python cmake pyside
$ pip install --user click yubioath-desktop
$ echo "pinentry-program $(which pinentry-mac)" >> $HOME/.gnupg/gpg-agent.conf
$ echo "PATH=$HOME/Library/Python/2.7/bin:$PATH" >> $HOME/.bash_profile
$ source .bash_profile
$ ln -s /usr/local/bin/gpg2 /usr/local/bin/gpg
```

### Windows

GUI Only: https://developers.yubico.com/yubioath-desktop/Releases/

GUI + CLI Tools:
```
C:\> choco install pip yubikey-personalization-tool gpg4win openssh
C:\> pip install --user yubioath-desktop
```

### Linux

#### Arch

```bash
$ pacman -S gpg yubikey-personalization pcsc-tools pcsclite libusb-compat libu2f-host swig gcc python2-pyside python2-click
$ sudo systemctl enable --now pcscd.service
$ pip install --user yubioath-desktop
```

#### Debian

```bash
$ apt-get install yubikey-personalization yubikey-personalization-gui gpgv2 pinentry-gtk2 swig pyside python-pip
$ pip install --user yubioath-desktop
```

#### Ubuntu

```bash
$ sudo add-apt-repository ppa:yubico/stable
$ sudo apt-get update
$ apt-get install yubikey-personalization yubikey-personalization-gui pcscd scdaemon gnupg2 pcsc-tools gpgv2 pinentry-gtk2 swig python-pip
$ pip install --user yubioath-desktop
```

You may want to create a symbolic link between your existing (obsolete) installation of `gpg` and link it to the more recent `gpg2`:

```bash
sudo ln -s /usr/bin/gpg2 /usr/bin/gpg -f
```

## Set PIN

To proceed with GPG operations you must set user/admin pin codes for your key. It is recommended these be different.
You will use the User pin day to day for things like SSH or GPG but you will only use the Admin pin in the event you lock yourself out or to change certain protected settings.

```
gpg2 --change-pin
> 3 # change Admin PIN
> 1 # change User PIN
```

## Set flags (Yubikey Only)

The following will enable security features on the Yubikey that are only relevant to engineers/developers and are not needed for browser-only workflows.

### Yubikey Neo / Yubikey Neo-n

Enable GPG mode:
```
$ ykpersonalize -m86
Firmware version 3.4.2 Touch level 1541 Program sequence 1

The USB mode will be set to: 0x86

Commit? (y/n) [n]: y
```

### Yubikey 4 / Yubikey 4 Nano

Require physical touch for all key operations:

```bash
$ wget "https://gist.githubusercontent.com/a-dma/797e4fa2ac4b5c9024cc/raw/dd9612337e91a4a3e212bbf72c8521c3efb1ea6b/yubitouch.sh"
$ shasum yubitouch.sh # should match dcd8a5ac295d186eedeafc33f3ff74e8d739065b
$ chmod +x yubitouch.sh
$ ./yubitouch.sh sig fix
$ ./yubitouch.sh aut fix
$ ./yubitouch.sh dec fix
```

## Dotfiles

Recent versions of `gnupg` >= 2.2.x, combined with the latest version of `systemd` will not require any dotfiles. However, for Ubuntu or Mac users that may be behind on versions or inability to use `systemd` will still need to use dotfiles for configuration.

At this point, you can probably get `gpg --card-status` to work, but in order for `ssh` operations (such as `ssh-add -L`) to be successful, you'll need a decent `.gnupg` dotfiles setup.

For a good Linux-specific setup, use [lrvick](https://github.com/lrvick/)'s [gnupg setup](https://github.com/lrvick/dotfiles/tree/master/.gnupg)
For a decent Linux/OS X combination setup, use [singlerider](https://github.com/singlerider/)'s [gnupg setup](https://github.com/singlerider/dotfiles/tree/master/.gnupg) combined with a comparable [.zshrc](https://github.com/singlerider/dotfiles/tree/master/.zshrc) or `.bashrc`.

```bash
$ cp -r dotfiles/.gnupg ~/
```
