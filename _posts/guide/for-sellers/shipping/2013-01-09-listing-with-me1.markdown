---
layout: guides
title: Listing with shipping me1
categories:
- listing
- manage listings
- orders
- manage questions
- shipping
menu:
- Listing &amp; Selling
tags:
- shipping
---

## Overview

This guide explains how to deal with all the resources of our API to successfully list and manage items with shipping mode **me1**.
It also explains how to post tracking numbers to provide the buyers with the tracking information.

Selected sellers are marked for shipping mode me1 internally after an agreement between the seller and ML.

The guide covers in detail the API resources involved:

<ul class="ch-list">
<li> shipping modes</li>
<li> items</li>
<li> shipping methods</li>
</ul>
<br/>

<div class="contents">
  <h5>Table of Contents</h5>
  <dl>
    <dt><a href="javascript:void(0)" onClick="goToByScroll('shipping-modes')">Verify the shipping mode</a></dt>
    <dt><a href="javascript:void(0)" onClick="goToByScroll('dimensions')">Dimensions</a></dt>
    <dt><a href="javascript:void(0)" onClick="goToByScroll('free-shipping')">Free Shipping</a></dt>
    <dt><a href="javascript:void(0)" onClick="goToByScroll('cost-calculator')">Cost Calculator</a></dt>
    <dt><a href="javascript:void(0)" onClick="goToByScroll('shipping-services')">Supported Shipping Services</a></dt>
    <dt><a href="javascript:void(0)" onClick="goToByScroll('tracking')">POST the tracking number</a></dt>
  </dl>
</div>



## Verify the shipping mode {#shipping-modes}
Use the **shipping_modes** resource to verify if a seller can list an Item on a given category with certain dimensions.

The response indicates if shipping modes me1 is available and the shipping methods that can be used.

<br />

### me1 mode

**URL**

<pre class="terminal">
https://api.mercadolibre.com/users/:user_id/shipping_modes?category_id=MLB74723&amp;dimensions=10x50x100,5000
</pre>


**Response**

{% highlight javascript %}
[
   {
    "mode": "me1",
    "shipping_attributes":  {
      "dimensions": "required",
      "costs": "not_allowed",
      "accepted_methods":  [
        182,
        100009
      ]
    }
  }
]
{% endhighlight %}

### Shipping methods
- -**182** represents the 'Express' method. Packages can take up to 3 days to be delivered.
- -**100009** represents the 'Standard' method. Packages take more than 4 days to be delivered.

<br />

**Response Attributes**
- `accepted_methods` — An array with the available shipping methods
- `dimensions` (required) — Seller has to provide dimensions when listing the item.
- `costs` (not allowed) — The seller is not allowed to set its own shipping costs.


<br />
<br />

### not specified mode
The dimensions have some restrictions e.g.: 30kg maximum weight.
 If there are no modes that support the given dimensions the item has to be listed with **not_specified** shipping.

<pre class="terminal">
https://api.mercadolibre.com/users/:user_id/shipping_modes?category_id=MLB74723&amp;dimensions=10x50x100,30001
</pre>


**Response**

{% highlight javascript %}
[
   {
      "mode":"not_specified",
      "shipping_attributes":{
         "dimensions":"optional",
         "costs":"not_allowed",
         "accepted_methods":[

         ]
      }
   }
]
{% endhighlight %}


## Item Dimensions {#dimensions}

Dimensions are used to calculate shipping the cost. If any measure is missing it is considered invalid.

Length, Width and Height are separated by 'x' and ',' separates the weight.

(Length cm)**x**(Width cm)**x**(Height cm)**,**(Weight gr)<br /><br />

Restrictions:
- **Weight** < 30000  (30 kg)
- L + W  + H <= 200

Dimensions should represent the size of the package that will be shipped. All values are mandatory.

Examples:

**10x20x50,3000** is a valid dimension string

**20x44x,3000** is an invalid dimension string


![Package dimensions](/images/shipping-box.jpg)

<br />

## Listing an item on me1 mode

It's quite simple to list an item with me1. Post the free shipping options and dimensions of the package whenever they are available.
If sellers do not provide package dimensions on their listing, the category standard dimensions will be used.

**URL to POST**
<pre class="terminal">
https://api.mercadolibre.com/items?access_token=
</pre>


**JSON for the body**
{% highlight javascript %}
{
   "title":"Item de teste",
   "category_id":"MLB74723",
   "price":10,
   "currency_id":"BRL",
   "available_quantity":10,
   "buying_mode":"buy_it_now",
   "listing_type_id":"bronze",
   "condition":"new",
   "description":"Item:, <strong> Ray-Ban WAYFARER Gloss Black RB2140 901 </strong> Model: RB2140. Size: 50mm. Name: WAYFARER. Color: Gloss Black. Includes Ray-Ban Carrying Case and Cleaning Cloth. New in Box",
   "pictures":[
      {
         "source":"http://upload.wikimedia.org/wikipedia/commons/f/fd/Ray_Ban_Original_Wayfarer.jpg"
      },
      {
         "source":"http://en.wikipedia.org/wiki/File:Teashades.gif"
      }
   ],
   "shipping":{
      "local_pick_up":false,
      "dimensions":"10x10x20,700"
   }
}
{% endhighlight %}

<br />

### Local Pick Up

The **local_pick_up** attribute enables the option for buyers to choose to pick up the item from the store and don't incur in shipping costs.

Set local_pick_up to *true* for sellers that offer this option to buyers.


## Adding dimensions to existing listings

After being marked with me1 mode, you can add dimensions and shipping to your existing listings.

Altering the dimensions of an item doesn't affect its relevance in search results and there is no restricion to alter dimensions if the item has sales.

Just do a PUT to items as in the example.

**URL to PUT**
<pre class="terminal">
https://api.mercadolibre.com/items/:item_id?access_token=
</pre>


**JSON for the body**
{% highlight javascript %}
{
   "shipping":{
      "dimensions":"10x10x20,700",
      "mode": "me1"
   }
}
{% endhighlight %}



## Free shipping {#free-shipping}

Sellers have the option to list items offering one of the *shipping methods* for *free*.

This type of shipping has some benefits: it is a superior shopping experience for the buyer, it is highlited in the search results and buyers can filter listings that offer free shipping.

*NOTE:* For the moment the only option for free shipping is "country"


To list offering Standard shipping for free with the country option, add the shipping method array with the attribute "free": "country".

**JSON for the body with free option**

{% highlight javascript %}
{
   "title":"Item de teste",
   "category_id":"MLB74723",
   "price":10,
   "currency_id":"BRL",
   "available_quantity":10,
   "buying_mode":"buy_it_now",
   "listing_type_id":"bronze",
   "condition":"new",
   "description":"Item:, <strong> Ray-Ban WAYFARER Gloss Black RB2140 901 </strong> Model: RB2140. Size: 50mm. Name: WAYFARER. Color: Gloss Black. Includes Ray-Ban Carrying Case and Cleaning Cloth. New in Box",
   "pictures":[
      {
         "source":"http://upload.wikimedia.org/wikipedia/commons/f/fd/Ray_Ban_Original_Wayfarer.jpg"
      },
      {
         "source":"http://en.wikipedia.org/wiki/File:Teashades.gif"
      }
   ],
   "shipping":{
      "local_pick_up":false,
      "methods":  [
        {
        "id": 100009,
        "free": "country",
        }
      ],
      "dimensions":"10x10x20,700"
   }
}
{% endhighlight %}


## Shipping Cost Calculator {#cost-calculator}

The API has a specific resource to calculate shipping costs for a given dimension and zip code destination.

It can be useful to check the reference prices to the most common destinations e.g.: To São Paulo or RJ.


There are 2 resources for the shipping calculator to choose the one that better suits your available parameters, both of them return the same result.


**URL**
<pre class="terminal">
https://api.mercadolibre.com/users/:user_id/shipping_options?category_id=:category_id&amp;dimensions=:dim&amp;zip_code=13565905
</pre>

<pre class="terminal">
https://api.mercadolibre.com/items/:item_id/shipping_options?zip_code=13565905
</pre>


**Response**

{% highlight javascript %}
{
  "destination": - {
    "zip_code": "01001000",
    "city": - {
      "id": "BR-SP-44",
      "name": "São Paulo",
    },
    "state": - {
      "id": "BR-SP",
      "name": "São Paulo",
    },
    "country": - {
      "id": "BR",
      "name": "Brasil",
    },
    "extended_attributes": - {
      "address": "Praça da Sé",
      "owner_name": null,
      "zip_code_type": - {
        "type": "LO",
        "description": "Logradouro",
      },
      "city_type": "CP",
      "city_name": "São Paulo",
      "version": 6,
    },
  },
  "options": - [
    - {
      "id": 18310062,
      "name": "Normal",
      "currency_id": "BRL",
      "list_cost": 13.86,
      "cost": 13.86,
      "tracks_shipments_status": "not_verified",
      "display": "recommended",
      "speed": - {
        "shipping": 72,
        "handling": 24,
      },
    },
    - {
      "id": 18310061,
      "name": "Expresso",
      "currency_id": "BRL",
      "list_cost": 14.88,
      "cost": 14.88,
      "tracks_shipments_status": "not_verified",
      "display": "always",
      "speed": - {
        "shipping": 24,
        "handling": 24,
      },
    },
  ],
}

{% endhighlight %}


**Attribute description**

- `currency_id`: The currency in which the price is charged.
- `list cost`: The cost for this shipping option.
- `cost`: The actual cost to be paid, for "free shipping" cost is 0.
- `tracks_shipments_status`: Indicates how this method may be tracked.
- `tracks_shipments_status.verified` : Can be internally tracked.
- `tracks_shipments_status.not_verified` : Tracking information must be provided by seller.
- `tracks_shipments_status.no` : Cannot be tracked.
- `speed.shipping`: Promise of time to deliver, expressed in hours.
- `speed.handling`: Promise of handling time, expressed in hours.



## Shipping Services {#shipping-services}
Obtain the list of supported shipping services.
Later on, sellers will be able to provide automatic tracking for one of the supported shipping services.
If the shipping service is not in this list, automatic tracking is not supported.

<pre class="terminal">
https://api.mercadolibre.com/sites/MLB/shipping_services
</pre>

{% highlight javascript %}
[
  - {
    "id": 1,
    "name": "PAC",
    "description": "PAC",
    "status": "active",
    "shipping_company": - {
      "id": 100008,
      "name": "Correios",
    },
    "site_id": "MLB",
    "tracks_shipments": true,
    "tracking_url": "http://websro.correios.com.br/sro_bin/txect01$.QueryList?P_LINGUA=001&P_TIPO=001&P_COD_UNI=#{trackingNumber}",
  },
  - {
    "id": 2,
    "name": "Sedex",
    "description": "Sedex",
    "status": "active",
    "shipping_company": - {
      "id": 100008,
      "name": "Correios",
    },
    "site_id": "MLB",
    "tracks_shipments": true,
    "tracking_url": "http://websro.correios.com.br/sro_bin/txect01$.QueryList?P_LINGUA=001&P_TIPO=001&P_COD_UNI=#{trackingNumber}",
  },
]
{% endhighlight %}



## Post the tracking number {#tracking}
It is an essential part of this mode that sellers provide tracking numbers allowing the buyers to be aware of the status of their packages and when is estimated to be delivered.

All you have to do is a PUT to the shipment with the `service_id` and `tracking_number` attributes.

<pre class="terminal">
https://api.mercadolibre.com/shipments/:shipment_id?access_token=
</pre>

{% highlight javascript %}
{
  "tracking_number": "TR1234567891",
  "service_id": 1
}
{% endhighlight %}

**Note:** If the product was sent by some another service, you should send the parameter `service_id` with value **11**.


##Error Codes Reference {#error-codes}

Check <a href="/shipping-overview/#error-codes">this link</a> for most common errors. For further assistance, please check our <a href='/community' target='_blank'>forums</a> or connect to our irc channel (#meli@irc.freenode.net). In case you find an issue in our API, please report it on <a href='https://github.com/mercadolibre/api/issues' target='_blank'>GitHub</a>.



