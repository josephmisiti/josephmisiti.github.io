---
layout: post
title:  "Setting Up Mandrill Webhooks With HTTPS"
date:   2014-11-03
categories: encryption
---

I ran into an issue recently trying to utilize [Mandrill's web hooks](https://mandrillapp.com/settings/webhooks) on a site forcing HTTPS (duh). After sending the test webhook from the web interface, I saw the following error:

{% highlight python %}
Webhook failed with error: POST to 
http://staging.mysite.com/invites/mandrill/callback failed: 
SSL certificate problem, verify that the CA cert is OK. 
Details: error:14090086:SSL 
routines:SSL3_GET_SERVER_CERTIFICATE:
certificate verify failed
{% endhighlight %}

After doing a bit of internet searching, and testing different server configurations, I figured out that I did not concatentate my [SSL certificates correctly](https://support.comodo.com/index.php?/Default/Knowledgebase/Article/View/789/37/certificate-installation-nginx).

I bought my SSL cert from [Comodo](https://ssl.comodo.com/), and after paying for the certs I received four files: `AddTrustExternalCARoot.crt`, `STAR_mysite_com.crt`,`COMODORSAAddTrustCA.crt`	and `COMODORSADomainValidationSecureServerCA.crt`	

The final solution was to concatentate them together in the following order:

{% highlight python %}
cat STAR_mysite_com.crt COMODORSADomainValidationSecureServerCA.crt 
AddTrustExternalCARoot.crt COMODORSAAddTrustCA.crt > ssl-bundle.crt
{% endhighlight %}


And finally, place the following blog in your NGINX config:

{% highlight python %}

server {

	listen	443 default_server ssl;
	client_max_body_size 4M;
	server_name _;

	ssl on;
	ssl_certificate			/home/ubuntu/.keys/ssl-bundle.crt;
	ssl_certificate_key		/home/ubuntu/.keys/mysite.com.key;
	
	....
}

{% endhighlight %}

Restart your NGINX server, and the test webhook will be successful!

P.S - Fuck Dev Ops