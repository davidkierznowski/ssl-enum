# Introduction #

SSL-Enum is a standalone SSL/TLS scanner.

There are a number of SSL cipher enumeration tools but (that I've seen) rely on OpenSSL or SSLeay and are not very flexible.

SSL-Enum hopes to become a flexible, standalone SSL inspection tool.

Please be aware the tools is still very much BETA and contributing is welcome and encouraged.

# Portability #
ssl-enum should compile on any ANSI C compatible system. It has compiled and run on the following platforms:
  * Linux 2.6.15-23-386
  * Windows Vista Home Edition (with CYGWIN)


# Installation #
$ make

# Usage #
$ ./ssl-enum -s 127.0.0.1 -p 443 (opt: -vf)
> -v 1,2
> > Level 1 debugging will print SSL server errors if available.

> -v 3
> > Level 2 debugging will print read and write data.

> -v 4
> > Level 4 allows SSLv3 packet decodes

> -f filename
> > Allows alternate ciphers file. The default is cipher-list.txt.

> -k fast SSL/TLS sweep
> > Some SSL/TLS services don't respond with errors so we assume its
> > an UNSUPPORTED cipher. To complicate matters, some Apache web servers
> > respond with an ASCII error text message so we can't check if we are
> > really talking to an SSL/TLS service.


> So we'll use a cautious flag. If the flag is set, we'll stop scanning,
> if the returned data does not match standard SSL/TLS data.

> Note: The default will be to scan all ciphers in the chosen/default
> ciphers file.

# Example Usage #
ssl-enum will use the weak-ciphers.txt file by default, however, we manually specify it in the following example to demonstrate file usage:
```
$ time ./ssl-enum -s x.x.x.x -p 443 -f weak-ciphers.txt
0x020080        SSL2_RC4_128_EXPORT40_WITH_MD5  SSL_EXPORT
0x040080        SSL2_RC2_CBC_128_CBC_EXPORT40_WITH_MD5  SSL_EXPORT
0x03    SSL3_RSA_RC4_40_MD5     SSL_EXPORT
0x06    SSL3_RSA_RC2_40_MD5     SSL_EXPORT
0x08    SSL3_RSA_DES_40_CBC_SHA SSL_EXPORT
0x14    SSL3_EDH_RSA_DES_40_CBC_SHA     SSL_EXPORT
0x60    SSL3_RSA_EXPORT1024_WITH_RC4_56_MD5     SSL_EXPORT
0x61    SSL3_RSA_EXPORT1024_WITH_RC2_CBC_56_MD5 SSL_EXPORT
0x62    SSL3_RSA_EXPORT1024_WITH_DES_CBC_SHA    SSL_EXPORT
0x03    TLS1_RSA_RC4_40_MD5     SSL_EXPORT
0x06    TLS1_RSA_RC2_40_MD5     SSL_EXPORT
0x08    TLS1_RSA_DES_40_CBC_SHA SSL_EXPORT
0x14    TLS1_EDH_RSA_DES_40_CBC_SHA     SSL_EXPORT
0x60    TLS1_RSA_EXPORT1024_WITH_RC4_56_MD5     SSL_EXPORT
0x61    TLS1_RSA_EXPORT1024_WITH_RC2_CBC_56_MD5 SSL_EXPORT
0x62    TLS1_RSA_EXPORT1024_WITH_DES_CBC_SHA    SSL_EXPORT
(Total ciphers checked: 75)

real    0m11.305s
user    0m0.000s
sys     0m0.024s
```

# SSL Resumption Check #
If SSL sessions are disabled, every connection (images, CSS and all) will require re-negotiation of SSL Ciphers and Certificates. SSL sessions can greatly reduce bandwidth and memory requirements.

The following output demonstrates an SSL service with SSL sessions CORRECTLY configured:
  * ./ssl-enum -s IP -p 443 -v4
```
--snip--
SESSION LEN: 32
SESSIONID: 2c3d8c8f6fb747a779fcedfe14129b8bd8605e72f75a6c64b728e81c01d64f04
--snip--
```

The following output demonstrates an SSL service with SSL sessions INCORRECTLY configured:
  * ./ssl-enum -s IP -p 443 -v4
```
--snip--
SESSION LEN: 0
SESSIONID: NULL
--snip--
```