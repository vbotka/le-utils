le-utils
========
[![license](https://img.shields.io/badge/license-BSD-red.svg)](https://www.freebsd.org/doc/en/articles/bsdl-gpl/article.html)

Utilities to manage letsencrypt.org certificates.

Installation and configuration tested with https://galaxy.ansible.com/vbotka/leutils/

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

Example of dry-run renewal
--------------------------

```
# lectl -n -c example.com
[OK]  lectl:  LEROOTMDATE=1533608521 stored
[OK]  lectl:  APACHE status: apache24 is running as pid 92877.
[OK]  lectl:  APACHE stopped
[OK]  lectl:  example.com expires in 61 days. Due for renewal.
[OK]  lectl:  Log: 
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator standalone, Installer None
Cert not due for renewal, but simulating renewal for dry run
Renewing an existing certificate
Performing the following challenges:
tls-sni-01 challenge for example.com
Waiting for verification...
Cleaning up challenges
IMPORTANT NOTES:
 - The dry run was successful.
[OK]  lectl:  Renewal of example.com finished. Check the permissions, or run: lectl -p
[OK]  lectl:  LEROOTMDATE=1533608521 restored
[OK]  lectl:  APACHE restarted
```

Notes
-----

1) certbot certonly vs. renew
* *certbot certonly* can "Obtain or renew a certificate, but do not install it"
* *certbot renew* can "Renew all previously obtained certificates that are near expiry"

2) Rate Limits for Let’s Encrypt                              
https://letsencrypt.org/docs/rate-limits/
* 20 certificates per domain per week
* 5 certificates per FQDN set per week
* 100 names per certificate. A certificate with multiple names is often called a SAN certificate, or sometimes a UCC certificate.
* The number of registrations you can make in a given time period; currently 500 per 3 hours                                                                                                   
  
3) Lifetime of the certificate (Pros and cons of 90-day certificate lifetimes)                                           
https://community.letsencrypt.org/t/pros-and-cons-of-90-day-certificate-lifetimes                                             The Technical Advisory Board chose                                                                                           * 90-day certificate lifetime to start with                                                                                   * with an expectation that people will want to auto-renew at the 60-day mark.                                                  
4) Certbot will not renew a certificate more then 30 days before expiration. Message: Cert not yet due for renewal.

5) Daily dry-run renewal is recommended. "This verifies whether you're apparently able to get a certificate, in your current configuration ... You can use this to simulate what would apparently happen if you ran the command without --dry-run." [Q. What's the new --dry-run flag?] (https://community.letsencrypt.org/t/help-us-test-renewal-with-letsencrypt-renew/10562)

