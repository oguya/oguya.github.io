---
layout: post
title:  "Validity of SSL Certificates"
date:   2014-11-18 18:25:45
excerpt: "openssl provides several options implementing SSL & TLS network protocols that you can use to communicate to a remote server & get more crypto related info about the server. In this blog post, we'll turn our attention to SSL certificates & the priceless information they contain."
category: Linux
tags: blog
---

An SSL certificate<sup>[[1]](http://en.wikipedia.org/wiki/X.509)</sup> contains a lot of information such as:

  - issuer
  - subject i.e. the owner
  - validity dates
  - fingerprints
  - certificate chain
  - and many more.

Openssl is a powerful cryptography & SSL/TLS toolkit that is readily available on most linux distros. It provides several options such as `s_client`<sup>[[2]](https://www.openssl.org/docs/apps/s_client.html)</sup> which allows you to connect to a remote host using SSL/TLS protocols, and `x509`<sup>[[3]](https://www.openssl.org/docs/apps/x509.html)</sup> which can be used to display certificate information, convert certificates to various forms, sign certificate requests like a 'mini CA' or even edit certificate trust settings. Using openssl, we'll obtain all the above information from an ssl certificate.


## Show validity
- show the expiry date of an ssl certificate:
{% highlight sh %}
echo | openssl s_client -connect site:port 2>/dev/null | openssl x509 -noout -dates
{% endhighlight %}

- For example:
{% highlight text %}
$ echo | openssl s_client -connect google.com:443 2>/dev/null | openssl x509 -noout -dates
notBefore=Sep 10 14:03:47 2014 GMT
notAfter=Dec  9 00:00:00 2014 GMT
{% endhighlight %}

## Show issuer
- show who issued the certificate i.e. Certificate Authority(CA)
{% highlight sh %}
echo | openssl s_client -connect site:port 2> /dev/null | openssl x509 -noout -issuer
{% endhighlight %}

- For example:
{% highlight text %}
$ echo | openssl s_client -connect google.com:443 2> /dev/null | openssl x509 -noout -issuer
issuer= /C=US/O=Google Inc/CN=Google Internet Authority G2
{% endhighlight %}

## Show subject
- show the owner/subject of the certificate
{% highlight sh %}
echo | openssl s_client -connect site:port 2> /dev/null | openssl x509 -noout -subject
{% endhighlight %}

- For example:
{% highlight text %}
$ echo | openssl s_client -connect google.com:443 2> /dev/null | openssl x509 -noout -subject
subject= /C=US/ST=California/L=Mountain View/O=Google Inc/CN=*.google.com
{% endhighlight %}

## Show validity period
- show both Before(`-enddate`) and After(`-startdate`) dates i.e. for what dates is the certificate valid ?
{% highlight sh %}
echo | openssl s_client -connect site:port 2> /dev/null | openssl x509 -noout -dates
{% endhighlight %}

- For example:
{% highlight text %}
$ echo | openssl s_client -connect google.com:443 2> /dev/null | openssl x509 -noout -dates
notBefore=Sep 10 14:03:47 2014 GMT
notAfter=Dec  9 00:00:00 2014 GMT
{% endhighlight %}

## All the above
- You can combine `-dates`, `-issuer` and `-subject` options in one command:
{% highlight sh %}
echo | openssl s_client -connect site:port 2> /dev/null | openssl x509 -noout -dates -issuer -subject
{% endhighlight %}

- For example:
{% highlight text %}
$ echo | openssl s_client -connect google.com:443 2> /dev/null | openssl x509 -noout -dates -issuer -subject
notBefore=Sep 10 14:03:47 2014 GMT
notAfter=Dec  9 00:00:00 2014 GMT
issuer= /C=US/O=Google Inc/CN=Google Internet Authority G2
subject= /C=US/ST=California/L=Mountain View/O=Google Inc/CN=*.google.com
{% endhighlight %}


## Show hash
- show cert's hash:
{% highlight sh %}
echo | openssl s_client -connect site:port 2> /dev/null | openssl x509 -noout -hash
{% endhighlight %}

- for example:
{% highlight text %}
$ echo | openssl s_client -connect google.com:443 2> /dev/null | openssl x509 -noout -hash
a18bd28a
{% endhighlight %}

## Show MD5 fingerprint
- print the certificate fingerprint:
{% highlight sh %}
echo | openssl s_client -connect site:port 2> /dev/null | openssl x509 -noout -fingerprint
{% endhighlight %}

- for example:
{% highlight text %}
$ echo | openssl s_client -connect google.com:443 2> /dev/null | openssl x509 -noout -fingerprint
SHA1 Fingerprint=8D:92:BB:69:54:E2:57:43:F3:4A:08:EC:35:96:0D:97:74:F5:66:6A
{% endhighlight %}

## Show all information in an SSL certificate
- Lets print all the information stored in a certificate:
{% highlight sh %}
echo | openssl s_client -connect  site:port 2> /dev/null | openssl x509 -noout -text
{% endhighlight %}

- for example
{% highlight text %}
[08:38] james@vast ~ $ echo | openssl s_client -connect google.com:443 2> /dev/null | openssl x509 -noout -text
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 2725601525394095740 (0x25d348258060be7c)
    Signature Algorithm: sha1WithRSAEncryption
        Issuer: C=US, O=Google Inc, CN=Google Internet Authority G2
        Validity
            Not Before: Sep 10 14:03:47 2014 GMT
            Not After : Dec  9 00:00:00 2014 GMT
        Subject: C=US, ST=California, L=Mountain View, O=Google Inc, CN=*.google.com
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:88:86:aa:db:2d:af:b3:7a:b6:20:99:79:b2:63:
                    a4:1a:0b:9f:18:40:3d:0f:bb:d3:df:f0:1c:97:dd:
                    84:8a:bf:1c:a1:f4:ac:36:9e:2b:f3:64:6f:8f:4e:
                    ca:5c:de:09:b4:31:5a:21:47:21:37:c8:86:c8:0a:
                    ae:5d:90:43:13:5a:ad:18:f0:e8:fa:2c:a3:d0:d4:
                    9a:68:d6:41:dc:c8:d3:3c:27:2e:4b:30:25:a3:12:
                    b4:36:b5:70:98:e2:f5:02:a2:dc:e8:bf:a2:06:02:
                    3e:bd:0a:71:69:52:04:37:d4:dc:af:64:11:af:ed:
                    d5:8c:4f:75:17:5d:04:5f:a4:d6:d0:ad:6a:da:45:
                    23:d0:3e:14:3a:3a:96:a5:e4:3d:1c:52:45:55:a3:
                    43:21:5c:41:71:9d:17:20:19:c9:f3:2c:18:f2:57:
                    45:87:2b:c8:0d:86:02:aa:79:3d:15:ed:b0:1b:37:
                    cf:5c:cd:4e:7c:75:50:63:d2:4b:8b:6b:d1:a4:e4:
                    23:90:d3:6f:c6:4d:b5:d2:3b:8b:3e:9c:eb:21:38:
                    ef:21:bb:13:00:db:09:f4:9e:91:ef:96:1f:78:f4:
                    b4:b6:b7:65:1c:90:12:75:8b:0d:12:90:c5:dd:55:
                    b5:77:a0:96:27:0f:a2:88:b5:71:b7:f2:21:7b:9e:
                    97:07
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Extended Key Usage:
                TLS Web Server Authentication, TLS Web Client Authentication
            X509v3 Subject Alternative Name:
                DNS:*.google.com, DNS:*.android.com, DNS:*.appengine.google.com, DNS:*.cloud.google.com, DNS:*.google-analytics.com, DNS:*.google.ca, DNS:*.google.cl, DNS:*.google.co.in, DNS:*.google.co.jp, DNS:*.google.co.uk, DNS:*.google.com.ar, DNS:*.google.com.au, DNS:*.google.com.br, DNS:*.google.com.co, DNS:*.google.com.mx, DNS:*.google.com.tr, DNS:*.google.com.vn, DNS:*.google.de, DNS:*.google.es, DNS:*.google.fr, DNS:*.google.hu, DNS:*.google.it, DNS:*.google.nl, DNS:*.google.pl, DNS:*.google.pt, DNS:*.googleadapis.com, DNS:*.googleapis.cn, DNS:*.googlecommerce.com, DNS:*.googlevideo.com, DNS:*.gstatic.cn, DNS:*.gstatic.com, DNS:*.gvt1.com, DNS:*.gvt2.com, DNS:*.urchin.com, DNS:*.url.google.com, DNS:*.youtube-nocookie.com, DNS:*.youtube.com, DNS:*.youtubeeducation.com, DNS:*.ytimg.com, DNS:android.com, DNS:g.co, DNS:goo.gl, DNS:google-analytics.com, DNS:google.com, DNS:googlecommerce.com, DNS:urchin.com, DNS:youtu.be, DNS:youtube.com, DNS:youtubeeducation.com
            Authority Information Access:
                CA Issuers - URI:http://pki.google.com/GIAG2.crt
                OCSP - URI:http://clients1.google.com/ocsp

            X509v3 Subject Key Identifier:
                A3:9E:4E:88:E8:51:57:6C:96:02:04:AD:C7:C8:9C:06:7C:BF:BF:41
            X509v3 Basic Constraints: critical
                CA:FALSE
            X509v3 Authority Key Identifier:
                keyid:4A:DD:06:16:1B:BC:F6:68:B5:76:F5:81:B6:BB:62:1A:BA:5A:81:2F

            X509v3 Certificate Policies:
                Policy: 1.3.6.1.4.1.11129.2.5.1

            X509v3 CRL Distribution Points:

                Full Name:
                  URI:http://pki.google.com/GIAG2.crl

    Signature Algorithm: sha1WithRSAEncryption
         39:25:0e:01:8c:a3:5e:14:37:82:c4:a3:dc:d4:16:d7:c3:6b:
         fd:e9:1c:20:93:6a:f6:91:47:8b:5c:15:73:3a:a1:71:27:68:
         76:56:b7:02:eb:fc:e7:9d:3c:8c:5a:69:a4:0b:75:ad:79:7e:
         0e:34:35:68:8f:7b:51:45:69:99:90:a2:f7:33:0c:54:37:d4:
         04:e9:4c:d9:d5:96:cb:b7:00:56:61:ec:27:ab:4a:21:54:15:
         10:f3:cc:6b:90:20:cd:ec:70:3a:f3:bc:fc:a0:be:c6:79:9a:
         f9:3c:1f:e0:cc:25:fa:ab:a2:8f:2f:06:36:26:16:c4:c4:41:
         64:cd:e3:c7:8a:7c:f6:d6:f0:25:bf:a7:94:76:66:4f:b0:56:
         5c:5f:c5:c9:86:4d:9b:49:07:8f:e3:4b:91:5c:c4:0d:e5:b3:
         6c:4d:1e:63:1f:94:4b:10:3c:df:8f:9c:d8:7f:19:56:6a:a7:
         b4:af:c1:69:81:ee:2f:f3:b7:fc:82:36:ce:72:2d:97:6f:9a:
         8f:d3:a7:6b:80:82:8b:59:ce:83:81:26:02:76:96:68:92:ac:
         36:93:01:4c:a4:55:91:89:65:6e:fc:b2:6d:90:c2:b3:63:75:
         8e:1e:ad:45:8a:d7:98:85:e2:b2:bf:c1:ca:e8:83:e8:98:82:
         be:3b:ba:19
{% endhighlight %}

## Conclusion
As a sysadmin, or an owner of a certificate, it's a good practice to check the expiry date on your certificate(s) once every 2weeks so that you can plan in advance & renew them before they expire.
This helps you avoid situations whereby your clients can't securely connect to your servers because of an expired certificate.

For me a simple cron job does the trick, every 2weeks it checks the validity of my certificates & emails me the results.
{% highlight sh %}
0   9   */14   *   1   echo | openssl s_client -connect $SITE:$PORTt 2> /dev/null | openssl x509 -noout -dates -issuer -subject|  mail -s "Certificate Validity for $SITE" $YOUR_EMAIL
{% endhighlight %}


### Links
1. [A bit of history on X.509](http://en.wikipedia.org/wiki/X.509)
2. [openssl s_client man pages](https://www.openssl.org/docs/apps/s_client.html)
3. [openssl x.509 man pages](https://www.openssl.org/docs/apps/x509.html)

