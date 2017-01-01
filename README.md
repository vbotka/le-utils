le-utils
========
[![license](https://img.shields.io/badge/license-BSD-red.svg)](https://www.freebsd.org/doc/en/articles/bsdl-gpl/article.html)

Utilities to manage letsencrypt.org certificates.

Installation and configuration tested with https://galaxy.ansible.com/vbotka/ansible-leutils/

Example of a renewal
--------------------

```
From: root@example.com (Cron Daemon)
To: root@example.com
Subject: Cron <root@example.com> /root/bin/lectl -D=30 -c -a                                                                                                         
Date: Tue, 27 Dec 2016 03:20:00 +0000 (UTC)

[OK]  lectl:  Due for renewal. example.com expires in 29 days.
[OK]  lectl:  Webserver: NONE
[OK]  lectl:  /usr/local/bin/certbot log:
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Cert is due for renewal, auto-renewing...
Starting new HTTPS connection (1): acme-v01.api.letsencrypt.org
Renewing an existing certificate
Performing the following challenges:
tls-sni-01 challenge for example.com
Waiting for verification...
Cleaning up challenges
Generating key (2048 bits): /usr/local/etc/letsencrypt/keys/0065_key-certbot.pem
Creating CSR: /usr/local/etc/letsencrypt/csr/0065_csr-certbot.pem

-------------------------------------------------------------------------------
Processing /usr/local/etc/letsencrypt/renewal/example.com.conf
-------------------------------------------------------------------------------

-------------------------------------------------------------------------------
new certificate deployed without reload, fullchain is
/usr/local/etc/letsencrypt/live/example.com/fullchain.pem
-------------------------------------------------------------------------------

Congratulations, all renewals succeeded. The following certs have been renewed:
  /usr/local/etc/letsencrypt/live/example.com/fullchain.pem (success)
  [OK]  lectl:  /usr/local/bin/certbot renew  finished. Check the permissions, or run: lectl -p

--
```

Steps after renewal
-------------------

Set permissions of private keys

```
$ lectl -p
```

Rebuild the FINGERPRINT file

```
$ leinfo -g -a
```

Rebuild the IDS data (https://github.com/vbotka/integrity)

```
$ integrity -r letsencrypt usr-local-etc
```
