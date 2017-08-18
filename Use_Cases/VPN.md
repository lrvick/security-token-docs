If you happen to be using a standard VPN system that allows authentication via X.509 certificates,it is possible to securely store these inside any smartcard that supports a PKCS12 interface.


There is not a lot of information around setting up Security Tokens with enterprise VPN systems such as F5, but such systems are not easy to audit and should be avoided if possible.

Here is a cached version of a now-deleted 2013 how-to article F5 dev blog posted on this:
http://webcache.googleusercontent.com/search?q=cache:53KnX9k3ZpMJ:https://devcentral.f5.com/articles/two-factor-authentication-using-yubikey-yubicloud-and-big-ip-ltm%2Byubikey+f5+vpn&hl=en&ct=clnk

There is at least one implementation of TOTP support for F5 you can roll yourself:
https://loadbalancing.se/2016/07/09/setting-up-apm-with-google-authenticator/

You could use Yubico Desktop or Yubico Authenticator to use a Yubikey to generate tokens if a setup like this was in place.

At the moment using the Secure Token backed SSH + reverse SSH tunnels may be your best bet for securely solving problems that require access to a private corporate network that has only closed remote access solutions that expose secrets in memory.

The main caveat of this is it will require a system already physically on the network you wish to to access to keep an outbound tunnel established you can connect through.

You could also eliminate the NAT by having an IPv6 IP address assigned to you via Hurricane Electric or similar. You could then ssh to an in-house system psudo-directly from the IPv6 internet.
