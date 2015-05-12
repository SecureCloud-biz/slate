---
title: dOctobat

language_tabs:
  - ruby
  - php: PHP

toc_footers:

includes:
  

search: false
---

# Introduction

Welcome to the [Octobat](https://www.octobat.com/) documentation. 

We advice you to follow this documentation when subscribing to Octobat.

# Environment
Please check that you can send us these fields.

Parameter | Description
--------- | -----------
customer_name | Company or individual name
business_type | B2C or B2B
eservice | Indicates if you are selling telecommunication, transmission or electronic services
customer_address | Your customer billing address, must be split into Street, zipcode, city, state and country
customer_ip_address **optional** | The IP address used by the customer to perform the purchase transaction
customer_vat_number **optional**  | Your customer registered tax identifier. Mandatory for B2B transactions





# Requirements
To ensure that all your invoices are correctly generated, you must follow this pattern for your Stripe API calls.


# Customers & Cards

> Add your transaction customer into Stripe

```ruby
customer = Stripe::Customer.create(
  :email => "foo@bar.io",
  :description => "John Doe Inc.",
  :metadata => {
    :business_type => "B2B",  # Can be B2B or B2C
    :vat_number => "FR60528551658",
    :ip_address => "87.176.10.2"
  }
)
```

```php
$customer = \Stripe\Customer::create(array(
  "email" => "foo@bar.io",
  "description" => "John Doe Inc.",
  "metadata" => array(
    "business_type" => "B2B", // Can be B2B or B2C
    "vat_number" => "FR60528551658",
    "ip_address" => "87.176.10.2"
  )
));
```

> Then create a card and fill the required billing data

```ruby

card = customer.sources.create(source: params[:stripeToken]) # params[:stripeToken] is obtained from Stripe.js

card.name = "John Doe"
card.address_line1 = "801 Via dei Condotti"
card.address_line2 = "Secondo piano"
card.address_city = "Roma"
card.address_zip = "10000"
card.address_state = ""
card.address_country = "Italia"
card.save
```

```php
$card = $customer->sources->create(array("source" => $POST['stripeToken]));

$card.name = "John Doe";
$card.address_line1 = "801 Via dei Condotti";
$card.address_line2 = "Secondo piano";
$card.address_city = "Roma";
$card.address_zip = "10000";
$card.address_state = "";
$card.address_country = "Italia";
$card.save();
```


### Arguments

Parameter | Default | Description
--------- | ------- | -----------
email **required** |  | Your customer email address
description **required** |  | Your customer name or company name
business_type **optional** | Your setting value in Octobat | B2C/B2B : possible value. This is a required Octobat field to compute the VAT rate for the transaction.
vat_number **optional** | null | Your customer Tax identification number. Mandatory if B2B transaction
ip_address **optional** | null | The IP address of the buyer


# Charges

```ruby
charge = Stripe::Charge.create(
  :amount      => 400,
  :currency    => "eur",
  :card        => card,
  :customer    => customer.id,
  :description => 'One time charge',
  :metadata => {
    :eservice => true
  }
)
```
```php
$charge = \Stripe\Charge::create(array(
  "amount"      => 400,
  "currency"    => "eur",
  "card"        => $card,
  "customer"    => $customer.id,
  "description" => 'One time charge',
  "metadata" => array(
    "eservice" => true
  )
))
```

<aside class="notice">
If you already know the VAT rate and don't need us to compute it for you, fill the metadata `vat_rate` field. Thus, you don't have to fill the customer `business_type` and the charge `eservice` metadata.
</aside>

### Arguments

Parameter | Default | Description
--------- | ------- | -----------
description **required** |  | The charge description, and the invoice object.
vat_rate **optional** | Octobat automatic computing | The VAT rate of this charge, if you already know it, and do not want Octobat to compute it for you.
eservice **optional** | Your setting value in Octobat | Will be processed if `business_type` equals to "B2C". This is a required Octobat field to compute the VAT rate for the transaction.


# Subscriptions

```ruby
customer.subscriptions.create(
  :plan => "octobat",
  :metadata => {
    :eservice => true
  }
)
```
```php
$customer->subscriptions->create(array(
  "plan" => "octobat",
  "metadata" => array(
    "eservice" => true
  )
))
```

Dealing with subscriptions is like dealing with simple charges, you must fill the metadata field to provide Octobat its required data to generate perfectly your invoices.

### Arguments

Parameter | Default | Description
--------- | ------- | -----------
vat_rate **optional** | Octobat automatic computing | The VAT rate of this charge, if you already know it, and do not want Octobat to compute it for you.
eservice **optional** | Your setting value in Octobat | Will be processed if `business_type` equals to "B2C". This is a required Octobat field to compute the VAT rate for the transaction.

# Invoice items

```ruby
Stripe::InvoiceItem.create(
  :customer => customer.id, # or customer.id
  :amount => 1000,
  :currency => "eur",
  :description => "Invoice description",
  :metadata => {
    :eservice => true # or false
  }
)
```
```php
Stripe::InvoiceItem.create(
  "customer" => $customer.id, # or customer.id
  "amount" => 1000,
  "currency" => "eur",
  "description" => "Invoice description",
  "metadata" => array(
    "eservice" => true # or false
  )
)
```

### Arguments

Parameter | Default | Description
--------- | ------- | -----------
vat_rate **optional** | Octobat automatic computing | The VAT rate of this charge, if you already know it, and do not want Octobat to compute it for you.
eservice **optional** | Your setting value in Octobat | Will be processed if `business_type` equals to "B2C". This is a required Octobat field to compute the VAT rate for the transaction.
