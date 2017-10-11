---
title: Bayo API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell: cURL
  - ruby: Ruby
  - php: PHP
  - javascript: JavaScript

toc_footers:
  - <a href='#'>Sign Up for a Developer API Key</a>
  - <a href='http://bayo.my'>Documentation Powered by Bayo Pay</a>

includes:
  - errors

search: true
---

# Introduction

BayoPay is a product by [Bayo Pay (M) Sdn Bhd](http://bayo.my), a company that is focusing on online / offline payment solutions. We aim to be a market leader as a Payment Facilitator on payment methods in Malaysia and globally.

## How Does It Works?

BayoPay is a service that helps merchant to sell online and expand rapidly to South-East Asia market. The service includes:

![alt text](images/how_does_it_works.png "Title")

### Frontend
- RWD or responsive web design payment page for online buyer to checkout.
- Channel switching is available for same currency channels.
- Common shopping carts payment module, plugin, add-on, or extension supported.

### Backend
- Callback to update merchant system on deferred status change.
- Merchant can login to control panel to track payment status.
- Scheduled report on daily / weekly / monthly basis to update merchant via email.
- Real-time visualize reports.

# Payment Flow Overview

BayoPay provides hosted payment page service. The integration is as simple as passing parameters via HTTPS POST method from merchant to BayoPay payment page. Buyer will proceed their transaction on internet banking or any payment channel. Once completed, BayoPay will redirect buyer’s front-end back to merchant  system,  using  POST  method.  IPN (Instant Payment Notification) or ACK from merchant could be implemented to confirm the receiving of payment status update.

![alt text](images/payment_flow_overview.png "Title")

## Handling Payment Notification

Merchant needs to prepare 3 simple and similar scripts to handle payment notification from BayoPay:

Script | Description
------ | -------
**Return URL** | For frontend or browser based notification, which are normally not a 100% reliable and robust channel due to unexpected network connectivity issue or client-side behaviour, such as browser application crash.
**Callback URL** | A special handler to get notified on non-realtime payment status, such as "deferred status update", change of payment status, or cash channel, which is not a real time payment naturally.

After the normal payment ﬂow, merchant can always send payment status query request, which is deﬁned in ReQuery APIs (a.k.a PSQ, Payment Status Query).

# Security & Data Integrity

BayoPay system uses “Niaga ID” & “Niaga Key” to generate encrypted hash to ensure data integrity in the payment process.

## Niaga Key

BayoPay Niaga Key is uniquely encrypted string for BayoPay merchants. It is a key or seed for generating one-time hash data, which are known as “bayo_reqhash” (from merchant to BayoPay) or “bayo_reshash” (from BayoPay to merchant).

> Example of how a Niaga Key looks like:

```shell
dT3jajIO93GH9shja83jl9a9w9hdfHY5
```

```ruby
dT3jajIO93GH9shja83jl9a9w9hdfHY5
```

```php
dT3jajIO93GH9shja83jl9a9w9hdfHY5
```

```javascript
dT3jajIO93GH9shja83jl9a9w9hdfHY5
```

### How to get the Niaga Key?
- Login to Bayopay Merchant Admin site.
- Go to Settings &rarr; Transaction Settings.
- Scroll down until you see "Niaga Key".
- Get the value and use it on any functions that require it.

<aside class="warning">
<code>dT3jajIO93GH9shja83jl9a9w9hdfHY5</code> is merchant’s Niaga Key provided by BayoPay. Please make sure it is at least 32 characters. Merchant or developer is advised not to disclose this secret key to the public. Once the key is compromised, please contact BayoPay immediately to reset the key.
</aside>

## Request Hash

BayoPay Request Hash or <code>bayo_reqhash</code> is to ensure the data integrity passed from merchant system to BayoPay payment page to avoid man-in-the-middle (MITM) attack. It becomes a mandatory for each transaction if “Enable Verify Payment” is activated in merchant proﬁle as shown.

> Formula to generate bayo reqhash:

```shell
bayo_reqhash = hash(sha256, niagaID + currency + orderID + amount + niaga_key);
```

```ruby
bayo_reqhash = hash(sha256, niagaID + currency + orderID + amount + niaga_key);
```

```php
bayo_reqhash = hash(sha256, niagaID + currency + orderID + amount + niaga_key);
```

```javascript
bayo_reqhash = hash(sha256, niagaID + currency + orderID + amount + niaga_key);
```

<aside class="notice">
<code><span style="color: #272822">bayo_reqhash</span></code> is encrypted using <code><span style="color: #272822">SHA256</span></code> encryption hash function and consists of the following information (must be set in the following orders):
<ul>
<li>BayoPay Niaga ID</li>
<li>Currency</li>
<li>Merchant Order ID</li>
<li>Transaction Amount (With 2 decimal place and without thousand)</li>
</ul>
</aside>

## Response Hash

BayoPay bayo_reshash is a returning hash string to ensure the data integrity passed back from BayoPay to merchant system. Merchant MUST verify this hash string properly and also compare the order ID, currency, amount, and the payment date/time if possible, to protect self-interest from being cheated by hacker buyer. bayo_reshash was encrypted twice using sha256 encryption hash function and consists of the following information (must be set in the following orders):

### First hash string
- Transaction ID
- Order ID
- Status
- BayoPay Niaga ID
- Amount
- Currency

> Formula to generate bayo reshash:

```shell
bayo_reshash = hash(sha256, bayo_tranID + bayo_orderID + bayo_status + bayo_niagaID + bayo_amount + bayo_currency);
```

```ruby
bayo_reshash = hash(sha256, bayo_tranID + bayo_orderID + bayo_status + bayo_niagaID + bayo_amount + bayo_currency);
```

```php
bayo_reshash = hash(sha256, bayo_tranID + bayo_orderID + bayo_status + bayo_niagaID + bayo_amount + bayo_currency);
```

```javascript
bayo_reshash = hash(sha256, bayo_tranID + bayo_orderID + bayo_status + bayo_niagaID + bayo_amount + bayo_currency);
```

### Final hash string
- Payment Date/Time
- BayoPay Niaga ID
- First hash string
- Approval Code
- BayoPay Niaga Key

#Sandbox Account

#Payment APIs

## Initiating Payment

Passing parameters to BayoPay payment page using POST method via HTTPS or SSL
connection will initiate a payment request from merchant system.

## Payment URL

- http://pay-BayoPay.fwcms.my/index.php 
- www.bayopay.com/index.php

The URL is an API to accept POST parameters from merchant site as well as the payment
page for buyer.

## Channel Lists

Channel Name | Parameter Name | Accepted Currency | Processing Amount Limit (RM) | Extra Information
------------ | -------------- | ----------------- | ---------------------------- | -----------------
Visa / MasterCard | creditmc | USD | 1.00 > x < 5000.00 | Multi-currency credit channel

## Payment Page Integration

This is the traditional integration method which will send the buyer information to BayoPay payment page.

## Parameters 

These parameters can be passed using either POST method. Please use UTF-8 encoding for all values.

Variable / Parameter | Type Format / Max Length | Description / Example
-------------------- | ------------------------ | ---------------------
<code>bayo_niagaid</code> | Mandatory, Alphanumeric. | Niaga ID provided by BayoPay
<code>bayo_currency</code> | Mandatory, 3 chars ISO- 4217 currency code. | Default payment currency from merchant site. E.g. MYR, USD, EUR SGD, CNY, IDR
<code>bayo_amount</code> | Mandatory, Decimal with 2 decimal points and without thousand separator. | The total amount to be paid in one purchase order. Conﬁgurable to lock this ﬁeld (Read-only). E.g. 500, 168.99
<code>bayo_orderid</code> | Mandatory, Alphanumeric up to 32 characters. | Invoice or order number from merchant system. Can set to Read-only ﬁeld. E.g. BH2018-09rev
<code>bayo_name</code> | Mandatory, Alphanumeric, 128 chars. | Buyer’s full name.
<code>bayo_email</code> | Mandatory, Email, 128 chars. | Buyer’s email address.
<code>bayo_mobile</code> | Mandatory, Alphanumeric, 32 chars. | Buyer’s mobile number or contact number.
<code>bayo_description</code> | Mandatory, Alphanumeric, 64kB. | Purchase itemized list or order description. Avoid special character so the request is not blocked by web application ﬁrewall.
<code>bayo_channel</code> | Mandatory, Predeﬁned string. | Default payment page will be displayed. Refer to Channel Lists.
<code>bayo_returnurl </code> | Optional, URL.  | Used for multiple return URL. URLs must be registered beforehand with BayoPay.
<code>bayo_callbackurl</code> | Optional, URL. | Used for multiple callback URL. URLs must be registered beforehand with BayoPay.
<code>bayo_reqhash</code> | Mandatory. 32 chars hexadecimal string. | This is the data integrity protection hash string. Refer bayo_reqhash section for details.

## Getting Result

Payment result will be returned to merchant system once payment is done or user abandons the payment process. HTTP POST is the only method that BayoPay returns all parameters to merchant’s return URL for real time status update, which merchant can conﬁgure it in merchant admin. Merchant system should block all other methods or parameters from untrusted source.

## Parameters

Variable / Parameter | Type Format / Max Length | Description / Example
-------------------- | ------------------------ | ---------------------
<code>bayo_amount</code> | 2 decimal points numeric value | The total amount paid.
<code>bayo_orderId</code> | Alphanumeric, 32 characters | Invoice or order number from merchant system.
<code>bayo_tranId</code> | 
<code>bayo_niagaId</code> 
<code>bayo_status</code> 
<code>bayo_bankcode</code> 
<code>bayo_paymentcode</code> 
<code>bayo_appcode</code> 
<code>bayo_errorcode</code> 
<code>bayo_errordesc</code> 
<code>bayo_currency</code> 
<code>bayo_channel</code> 
<code>bayo_txndate</code> 
<code>bayo_reshash</code> 








# Authentication

> To authorize, use this code:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
```

> Make sure to replace `meowmeowmeow` with your API key.

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint retrieves a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

