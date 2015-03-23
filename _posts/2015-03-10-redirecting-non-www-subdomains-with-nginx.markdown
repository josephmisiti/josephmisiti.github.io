---
layout: post
title:  "Re-directing non-www subdomains with NGINX"
date:   2015-03-10
---

Lets say you are hosting `www.example.com` with an NGINX front-end and you want to re-direct
`http://example.com` and `https://example.com` to `http://www.example.com`. Here is the NGINX config to do that


{% highlight python %}

server {
	listen 80;
	server_name example.com;
	return 302 https://www.example.com$request_uri;
}

server {
	listen 443;
	server_name example.com;
	return 302 https://www.example.com$request_uri;
}

server {
	listen	443 default_server ssl;
	client_max_body_size	4M;
	
	...

}

{% endhighlight %}

