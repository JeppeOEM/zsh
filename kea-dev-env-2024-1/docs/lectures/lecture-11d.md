# Lecture 11d: DNS and Certificates

In this lecture we will look at the *Domain Name System* (DNS) and web certificates.


## Domain Name System

The *Domain Name System* (DNS) is a system that allows for humans to access systems on the internet (and other networks) by *name*, rather than having to know their actual *IP addresses*.

The reason for this is very simple: it is far easier to remember a name like `kea.dk` than the IP address `89.34.18.61`.

It also allows us some flexibility to move resources between systems, by simply pointing the name to the new resource.

### A Records

DNS provides various types of records, each used for their own purpose.

!!! note inline end

    `A` records points to IPv4 addresses.

Probably the most important is the `A` record.
An `A` record points to a *domain name* or a *fully qualified domain name* (FQDN).

Let us take a look at a domain familiar to us:

```zsh
dig kea.dk
```

We will get some output like this:

!!! note inline end

    Sometimes, you will come across `AAAA` records. These are like `A` records, but for IPv6 addresses.

```text
; <<>> DiG 9.18.16 <<>> kea.dk
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 35781
;; flags: qr rd ra ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;kea.dk.				IN	A

;; ANSWER SECTION:
kea.dk.			3600	IN	A	89.34.18.61

;; Query time: 40 msec
;; SERVER: 139.162.130.16#53(139.162.130.16) (UDP)
;; WHEN: Wed Aug 02 15:18:32 CEST 2023
;; MSG SIZE  rcvd: 51
```

There is a lot of metadata here, but two sections are of special interest: the `QUESTION SECTION` and the `ANSWER SECTION`.

The `QUESTION SECTION` is basically the query we made against DNS, while the `ANSWER SECTION` provides the information we were looking for.

We can see that `kea.dk` has registered an `A` record pointing to `89.34.18.61`.
This is the same query your browser will make when you enter `kea.dk` in the address bar.

In web development, we use `A` records to point to hosts and services, e.g., web servers.


### SOA Records

Another interesting record is the `SOA` record, the *Start Of Authority*.

This record **must** list an administrative responsible contact.
If we check `kea.dk`:

```zsh
dig soa kea.dk
```

We get an answer section looking like this:

```text
;; ANSWER SECTION:
kea.dk.			3600	IN	SOA	ns.efif.dk. dns-zc.efif.dk. 2023012080 14400 3600 2419200 3600
```

This record tells us two email addresses we can contact if there is some kind of problem.
DNS can not show `@` in the response, so the first `.` must be substituted with an `@`.


### MX Records

The `MX` records indicated how email for this domain should be handled.

Let us again take a look at `kea.dk`:

```zsh
dig mx kea.dk
```

The answer looks like this:

```text
;; ANSWER SECTION:
kea.dk.			3600	IN	MX	0 kea-dk.mail.protection.outlook.com.
```

In this case, we must make a new request to see the actual address:

```zsh
dig kea-dk.mail.protection.outlook.com
```

Giving us:

```text
;; ANSWER SECTION:
kea-dk.mail.protection.outlook.com. 10 IN A	52.101.68.5
kea-dk.mail.protection.outlook.com. 10 IN A	52.101.68.0
kea-dk.mail.protection.outlook.com. 10 IN A	52.101.73.2
kea-dk.mail.protection.outlook.com. 10 IN A	52.101.73.6
kea-dk.mail.protection.outlook.com. 10 IN A	52.101.73.1
kea-dk.mail.protection.outlook.com. 10 IN A	52.101.73.4
kea-dk.mail.protection.outlook.com. 10 IN A	52.101.68.21
kea-dk.mail.protection.outlook.com. 10 IN A	52.101.68.3
```

All these hosts have a priority `10`, so email systems sending email to `kea.dk` will choose a random host of the ones listed above.


### TXT Records

The `TXT` records can contain arbitrary text information.
These records are often used to prove ownership of a domain.

Try to list the `TXT` records in `kea.dk` using this command:

```zsh
dig txt kea.dk
```

You will see something like this:

```text linenums="1" hl_lines="3-4"
;; ANSWER SECTION:
kea.dk.			3600	IN	TXT	"adobe-idp-site-verification=f567008a-0fd9-45e3-a435-571b4d9ec8be"
kea.dk.			3600	IN	TXT	"_globalsign-domain-verification=QK1jpurZ4DjTrgouhzN2CkdS2br4ZJ4umd1xaBuxLP"
kea.dk.			300	IN	TXT	"v=spf1 " "ip4:77.237.55.139 " "ip4:81.95.241.179 " "ip4:81.95.241.180 " "ip4:88.151.73.29 " "ip4:89.34.18.16 " "ip4:89.34.18.61 " "ip4:77.247.70.242 " "ip4:94.18.243.152 " "ip4:94.18.243.147 " "ip4:116.203.18.244 " "ip4:95.172.90.143 " "ip4:95.172.90.156  " "ip4:216.147.218.8/30 " "ip4:52.166.0.186 " "include:servers.mcsv.net " "include:oa.spf.comendosystems.com " "include:ob.spf.comendosystems.com " "include:nets1.spf.messagelabs.com " "include:nets2.spf.messagelabs.com " "include:spf.protection.outlook.com " "include:amazonses.com " "include:spf.topdesk.net " "include:_spf.emply.com " "-all"
kea.dk.			3600	IN	TXT	"MS=ms10551711"
kea.dk.			3600	IN	TXT	"OZVLy6BQQ1k3fz7Z2DpabTI643J0IiJZt39IQd2R3iD27L02vivsENJd/yW5NT35turjm3NQ35DmS1Hffkwzdw=="
kea.dk.			3600	IN	TXT	"KEA"
```

The lines highlighted above shows records used for email domain verification.
This is so that other mail systems can confirm the originating server of `kea.dk` emails are allowed to send `kea.dk` emails.


## Certificates

In the early days of the internet most traffic used the `HTTP` protocol for most non-confidential information.

As the internet has become an increasingly hostile place, nearly every resource is now hosted using the secure `HTTPS` protocol instead.

To use `HTTPS` we must have a *certificate*.

The certificate is issued to a particular host system on a particular IP address.
This means that other people can't just make their own `kea.dk` server and trick people to use that one instead.

!!! note inline end

    Not all certificates are equal. While Let's Encrypt offers free certificates, these certificates only demonstrate ownership of a domain name. There is no organizational verification behind such a certificate.

    Validated certificates are still pretty expensive, but often not necessary.

Until a few years ago, certificates were very expensive.
This has changed with providers such as [http://letsencrypt.org/](http://letsencrypt.org/), that offers free TLS certificates.

As indicated above, using certificates have become the norm.
You should always use certificates whenever possible, even if it is just a free certificate.

To obtain a certificate you must own and be in control of a *domain name*.
Domain names can be bought very cheap, but they are not free, as there is some central fees required.

If you need a cheap domain name you can often get `.xyz` domains for as little as one or two US Dollar at [https://www.namecheap.com](https://www.namecheap.com) and many other places.
