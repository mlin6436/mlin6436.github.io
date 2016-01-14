---
layout: post
title: "Subversion with Certificate-based Authentication"
tags: ["subversion", "svn", "certificate", "authentication", "p12", "pem", "crt", "key", "configuration"]
---

<div class="message">
Sometimes it is fashionable to move against the trend, such as using Subversion rather than Git. In order to keep the thing going, it requires a bit of black magic. Therefore, I will be explaining how to use Subversion with certificate based authentication to access your super secure server.
</div>

# Installing Subversion

This should make easy using brew from a Mac.

{% highlight bash %}
$ brew search subversion
{% endhighlight %}

It is important to make sure the version of Subversion installed is compatible with the subversion installed on the server. Subversion 1.7 and 1.8 (1.9 is the latest stable version but I can't really speak for it) are proven to be working interchangeably, source code checked in using 1.6 may not be easily upgraded.

# Certificate Conversion

A certificate is used in subversion server to verify client's identify.

Typically a certificate ([.p12](https://en.wikipedia.org/wiki/X.509) or [.pem](https://en.wikipedia.org/wiki/X.509)) should be assigned to you for this purpose, and it can be converted back and forth to other certificate formats using [openssl](https://www.openssl.org/) (more on the conversion in the next section).

In case you don't know what p12 and pem are, p12 is a certificate format which is fully encrypted and password protected. While pem certificate is just like p12 containing both public key and private key, but also root certificates.

Now, it is jolly good that you have your p12 with you. But a lot of times, you need more than just p12. In this case, a pem file is also required. As a quick guide, I have listed most common commands used to convert certificates.

### Convert p12 to pem

{% highlight bash %}
$ openssl pkcs12 -nodes -clcerts -in cert.p12 -out cert.pem
{% endhighlight %}

### Convert p12 to pem (with password)

{% highlight bash %}
$ openssl pkcs12 -in cert.p12 -out cert.pem
{% endhighlight %}

### Extract certificate from p12

{% highlight bash %}
$ openssl pkcs12 -nokeys -in cert.p12 -out cert.crt
{% endhighlight %}

### Extract key from p12

{% highlight bash %}
$ openssl pkcs12 -nocerts -in cert.p12 -out cert.key
{% endhighlight %}

### Generate p12 from cert and key

{% highlight bash %}
$ openssl pkcs12 -export -in foo.cert -inkey foo.key -out foo.p12
{% endhighlight %}

### Convert pem to p12

{% highlight bash %}
$ openssl pkcs12 -export -in original.pem -out new.p12
{% endhighlight %}

### Change p12 password

{% highlight bash %}
$ openssl pkcs12 -in original.p12 -nodes -clcerts -out temp.pem
$ openssl pkcs12 -export -in temp.pem -out new.p12
{% endhighlight %}





# Configure Subversion to Use Certificate-based Authentication

After installation, Subversion will create a directory structure like the following in home directory.

{% highlight bash %}
.subversion
├── README.txt
├── auth
│   ├── svn.simple
│   ├── svn.ssl.client-passphrase
│   ├── svn.ssl.server
│   └── svn.username
├── config
└── servers
{% endhighlight %}

In `servers` file, the following configurations are needed for the extra authentication.

{% highlight bash %}
[groups]
lemoncake = repo.lemoncake.com

[lemoncake]
ssl-authority-files = path/to/pem
ssl-client-cert-file = path/to/p12
ssl-client-cert-password = superpassword
# http-proxy-host = www.proxy.lemoncake.com
# http-proxy-port = 80
{% endhighlight %}

First of all, a `group` needs to be added with URL to the source control server.

Then in the group definition, a list of detailed settings (including cert and password) are required, with optional settings such as proxy.
