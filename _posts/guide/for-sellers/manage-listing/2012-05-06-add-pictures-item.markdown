---
layout: guides
title: Working with pictures
categories: 
- listing
- manage listings
- orders
- manage questions
- shipping
menu: 
- Listing &amp; Selling
tags: 
- manage listings
---

## Overview 

Pictures are optional when listing, but they make a big difference in terms of the quality and results of your listing (visits and contacts). This tutorial is going to show you how to add and upload pictures. 

Users can upload pictures up to 10 MB. Picture format can be .jpg, .jpeg, .png or .gif (without animation). 

### Zooming
For pictures wider than 800 pixels, a zoom widget is activated that lets buyers roll over and take a close-up look. This is highly recommended for Clothes and Real Estate properties.

## Upload the picture

The following POST will upload your picture to the MELI Storage.

<pre class="terminal">
curl -F file=@/home/user/picture.jpg

https://api.mercadolibre.com/pictures?access_token=$ACCESS_TOKEN
</pre>

It will return a JSON describing the picture details. Remember to save the picture “id”. The other fields represent different picture sizes.

{% highlight javascript %}
{
   "id":"MLA430387888_032012",
   "quality":"",
   "variations":[...]
}
{% endhighlight %}

## Link the picture to the Item

Using this picture “id” retrieved you can link the previous uploaded picture to your item.

(You have to provide the item “Id” and the Picture “id”)

<pre class="terminal">
curl -X POST -H "Content-Type: application/json" -H "Accept: application/json" -d
'{"id":"MLA430387888_032012"}'

https://api.mercadolibre.com/items/MLA421101451/pictures?access_token=$ACCESS_TOKEN
</pre>

That’s all!. Go to your item’s VIP (using the permalink field) and check the new picture.

Note: There is currently a maximum number of fifteen pictures per property.

##Error Codes Reference {#error-codes}

Check <a href="/list-your-item/#error-codes">this link</a> for most common errors. For further assistance, please check our <a href='/community' target='_blank'>forums</a> or connect to our irc channel (#meli@irc.freenode.net). In case you find an issue in our API, please report it on <a href='https://github.com/mercadolibre/api/issues' target='_blank'>GitHub</a>.
