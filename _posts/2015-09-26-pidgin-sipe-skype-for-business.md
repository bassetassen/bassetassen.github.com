---
layout: post
title: Setting up Pidgin for Skype for business
categories: [Linux, Pidgin]
tags: [Lync, Skype for business, Pidgin]
date: 2015-09-27 17:49:00 +0200
---
{% include JB/setup %}

At work we use Skype for business, formerly known as Lync, for IM. That works fine at work where I use a Windows 10 machine. But sometimes I want to use Skype for business to chat with some colleagues from home. So I wanted to get a Skype for business client on my home machine running Ubuntu.

After a little bit of googling I found that there was a plugin for [Pidgin](https://pidgin.im) called [pidgin-sipe](http://sipe.sourceforge.net). So I install Pidgin and Pidgin-sipe with apt-get.

```bash
sudo apt-get install pidgin pidgin-sipe
```

Then I open Pidgin and add an account. On the basic tab I used these settings:
- Protocol: Office Communicator
- Username: My email
- Password: My Password

On the advanced tab:
- Connection type: Auto
- Authentication scheme: TLS-DSK

When I'm connecting now, I get an error, saying unable to validate certificate. To fix this I go to this domain, download the certificate and put it in ~/.purple/certificates/x509/tls_peers/
It seems like it is important to name the certificate file the same as the domain, so I name the certificate file webext01.phonectuc.net
<img src="{{ site.url }}/assets/images/pidgin_sipe_setup/SSL_Certificate_Error.png" class="img-responsive img-right" alt="Messagebox with error" title="Unable to validate certificate" />

Now I get the same error with another domain, I can see in the certificate's alternative names section in the downloaded certificate, that this domain is listed there. So I only make a copy of the already downloaded certificate and rename it to the domain name.

And now I am able to login and chat with my colleagues. As of this writing I am using Pidgin 2.10.9 and Sipe 1.18.2
