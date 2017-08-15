# SSH

## Configure SSH to use your Security Token

This assumes you already have a Security Token configured with a GPG Authentication subkey.

The end result will be that you distribute the SSH Public Key from your Security Token to all VCS systems and servers you normally connect to via SSH. From there you will need to have your key inserted, and tap it once for every connection. You will also be required to tap the key for all SSH Agent forwarding hops removing many of the security issues with traditional ssh agent forwarding on shared systems.

### Configure gpg-agent to also behave as an ssh-agent:

```
echo >~/.gnupg/gpg-agent.conf <<HERE
enable-ssh-support
keep-display # helps avoid issues with pinentry
HERE
```

Additionally if you are using OSX you will want to set a custom program for pin entry:

```
echo "pinentry-program /usr/local/bin/pinentry-mac" >> ~/.gnupg/gpg-agent.conf 
```

Edit your ~/.bash_profile (or similar) and add the following lines to use gpg-agent (and thus the Security Token) as your SSH key daemon:

```
# If connected locally
if [ -z "$SSH_TTY" ]; then

    # setup local gpg-agent with ssh support and save env to fixed location
    # so it can be easily picked up and re-used for multiple terminals
    envfile="$HOME/.gnupg/gpg-agent.env"
    if [[ ! -e "$envfile" ]] || ( \
           # deal with changed socket path in gnupg 2.1.13
           [[ ! -e "$HOME/.gnupg/S.gpg-agent" ]] && \
           [[ ! -e "/var/run/user/$(id -u)/gnupg/S.gpg-agent" ]]
       );
    then
        killall gpg-agent
        gpg-agent --daemon --enable-ssh-support > $envfile
    fi

    # Get latest gpg-agent socket location and expose for use by SSH
    eval "$(cat "$envfile")" && export SSH_AUTH_SOCK

    # Wake up smartcard to avoid races
    gpg --card-status > /dev/null 2>&1 

fi

# If running remote via SSH
if [ ! -z "$SSH_TTY" ]; then
    # Copy gpg-socket forwarded from ssh to default location
    # This allows local gpg to be used on the remote system transparently.
    # Strongly discouraged unless GPG managed with a touch-activated GPG 
    # smartcard such as a Yubikey 4.
    # Also assumes local .ssh/config contains host block similar to:
    # Host someserver.com
    #     ForwardAgent yes
    #     StreamLocalBindUnlink yes
    #     RemoteForward /home/user/.gnupg/S.gpg-agent.ssh /home/user/.gnupg/S.gpg-agent
    if [ -e $HOME/.gnupg/S.gpg-agent.ssh ]; then
        mv $HOME/.gnupg/S.gpg-agent{.ssh,}
    elif [ -e "/var/run/user/$(id -u)/gnupg/S.gpg-agent.ssh" ]; then
        mv /var/run/user/$(id -u)/gnupg/S.gpg-agent{.ssh,}
    fi

    # Ensure existing sessions like screen/tmux get latest ssh auth socket
    # Use fixed location updated on connect so in-memory location always works
    if [ ! -z "$SSH_AUTH_SOCK" -a \
        "$SSH_AUTH_SOCK" != "$HOME/.ssh/agent_sock" ];
    then
        unlink "$HOME/.ssh/agent_sock" 2>/dev/null
        ln -s "$SSH_AUTH_SOCK" "$HOME/.ssh/agent_sock"
    fi
    export SSH_AUTH_SOCK="$HOME/.ssh/agent_sock"
fi
```

Now switch to using this new setup immediately:

```
$ killall gpg-agent
$ source ~/.bash_profile
```

## Get SSH Public Key

You (or anyone that has your GPG public key) can get your SSH key as follows:

```
$ gpg --export-ssh-key you@email.com

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCcjW34jewR+Sgsp1Kn4IVnkb7kxwZCi2gnqMTzSnCg5vABTbG
jFvcjvj1hgD3CbEiuaDkDqCuXnYPJ9MDojwZre/ae0UW/Apy2vAG8gxo8kkXn9fgzJezlW8xjV49sx6AgS6BvOD
iuBjT0rYN3EPQoNvkuqQmukgu+R1naNj4wejHzBRKOSIBqrYHfP6fkYmfpmnPyvgxbyrwmss2KhwwAvYgryx7eH
HRkZtBi0Zb9/KRshvQ89nuXik50sGW6wWZm+6m3v6HNctbOI4TAE4xlJW7alGnNugt3ArQR4ic3BeIuOH2c/il4
4BGwdRIp7XlSkkf8cZX59l9kYLaDzw4Z cardno:000603703024
```

You can paste that into your list of authorized keys on all VCS and systems you have access to.

You should disable any other keys that are not backed by a Security Token.
