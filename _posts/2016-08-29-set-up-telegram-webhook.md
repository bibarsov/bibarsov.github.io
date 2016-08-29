---
published: true
layout: post
title: Telegram Bot API: Enable Webhook for your bot
date: 2016-08-29
categories: ruby-on-rails
---
## Telegram Bot API: Enable Webhook for your bot

### What is webhook?
Usually Telegram allows you to [get updates](https://core.telegram.org/bots/api#getupdates){:target="_blank"} for your bot. Of course, in that case you should do that manually, via HTTP GET request to Telegram. Besides, Telegram allows to "connect" your bot to their server, which could send updates to you (your provided https server) automatically. It known as "webhook".

### Getting started
Well, it's easy enough. First of all, you have to set up your server and make sure of that your server is ready to accept updates from Telegram. Also you must provide generated key, which can generated as described [herein](https://core.telegram.org/bots/self-signed){:target="_blank"}.
So, there a few simple ways to do that.

**Curl way**

	curl -F "url=https://YOUR_SERVER_URL" -F "certificate=@location/of/your/certificate.crt" https://api.telegram.org/botBOT_TOKEN/setWebhook

**PHP way**

{% highlight php %}
<?php

$url = "https://api.telegram.org/botBOT_TOKEN/setWebhook";

$ch = curl_init($url);
$post_data = Array(
	"url" => "https://YOUR_SERVER_URL",
	"certificate" => "@location/of/your/certificate.crt"
);
curl_setopt($ch, CURLOPT_POSTFIELDS, $post_data);
$result = curl_exec($ch);
echo $result;

?>
{% endhighlight %}
