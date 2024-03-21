# Lecture 1b: Up And Running


## Secure Shell (SSH)

During the semester, we will use the *Secure Shell* (SSH) to access a *virtual private server* (VPS).

To better understand what is going on, we will briefly talk about what SSH is.

Before we all started using SSH, we used something called [Telnet](https://www.ietf.org/rfc/rfc854.txt).
Telnet is a very simple protocol, that allows a user on a terminal emulator to access a shell on a remote system.

The problem with Telnet is that there is no security, so even passwords are transmitted in clear text, so if anyone is eavesdropping, they can log in themselves, and do whatever we can do on the remote system.
Because users are likely to reuse passwords, this would potentially let an eavesdropper access any system the user has access to.

To avoid these problems, [SSH](https://www.ietf.org/rfc/rfc4251.txt) is equipped with strong security.

### SSH Password-Based Authentication

The most basic form of authentication used with SSH is *password based authentication*.
Using password-based authentication, the user logs on with a set of credentials that consists of a username and a password.

Even if you have a very strong password, this is not a good way to authenticate.
This is because SSH must be accessible directly on the internet, so people will try to brute-force attack your VPS.


### SSH Key-Based Authentication

!!! warning inline end

    When using key-based authentication, make sure you keep your private key safe.

A much better way to authenticate is using *key-based authentication*.
In key-based authentication, the SSH protocol makes use of an *asymetric* key-pair: a *public* key and a *private* key.

As long as you do not expose your private key, this is an extremely secure authentication method.


### SSH Tunneling

The SSH protocol establishes an encrypted *tunnel*.
By default, SSH will run on port `22`.

Other than tunneling your remote shell, SSH can also tunnel arbitrary IP ports, say port `4000` and `8000`.
We can then run development servers on these ports, and access them as if these development servers were running on our local machine.


### SSH Files

There are a number of files that are of interest to SSH.

The SSH files are located in each users' home directory, in a hidden directory called `.ssh/`.

```text
.ssh
├── authorized_keys
├── config
├── know_hosts
├── id_ed25519
└── id_ed25519.pub
```

The `authorized_keys` file contains a list of **public keys** belonging to that user, but from **different hosts**.

If you check this file, you will see that it contains the **public key** from your local computer.

Since only the user has access to the `.ssh/` directory, he should be the only one able to add keys to this file, thus the user is in control of which keys gives access.

The `config` file can be used to list host systems, their IP addresses, and the parameters used for accessing each individual host system.

The `known_hosts` stores fingerprints of remote systems that this user on this system has accessed.
If you reinstall your `kea-dev` server you will get an error message, because the fingerprint from `kea-dev` no longer match the fingerprint stored in this file.
If you know that you have just reinstalled your server, this is expected behavior, and you can simply edit the file to delete the fingerprint from that particular system.

However, if you did not change anything, an error could indicate that something is wrong security wise.

Finally, we have the SSH key pair.
The key pair consists of a public key (ending with `.pub`), and the private key.
These are the keys for **this user** on **this system**.

If you want to use SSH authentication with other systems, e.g., GitLab, you will add the content of the **public key** to your user profile on that remote system.

The **SSH Server** also stores files for its configuration, typically in `/etc/ssh/`.
These files are already configured when you complete the installation of the `kea-dev` server, so you do not need to changes these files.


## Installing a Virtual Private Server (VPS)

!!! warning inline end "Very Important!"

    It is very important that you get the VPS up and running, as we will use it throughout the semester.

We are going to make use of a VPS throughout the semester, because:

- That way we can all use the same environment no matter what computer and OS we are using
- You will be exposed to working in a Linux terminal environment
- The VPS will have a real, public IP address that you can use for hosting your project

Please follow the instructions here to set up a VPS:

[https://gitlab.com/henrikstroem/vps-reference-config]( https://gitlab.com/henrikstroem/vps-reference-config)

## Vim-tutor

You will need to familiarize yourself with the `vim` editor to be able to work effectively and efficiently on the VPS.

Log in to your VPS as shown in the guide linked above, and run the command `vimtutor`.

The tutorial will introduce you to working with source files on the VPS.
