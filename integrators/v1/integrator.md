# Integration API - V1 - ALPHA 0.1

Note: this is a draft document that is open to suggestions and is bound to change.

At DAN.COM  we offer three types of integration through our api.

- Light
- Medium
- Advanced

With the light version the integrator can setup transactions on behalve of a seller on DAN.COM. In this case the transaction is setup by the integrator where DAN.COM will do payment collection and escrow for that transaction. The integrator will need the sellers `Dan Distribution Network ID`, which the seller can find in their [settings page](htttps://dan.com/users/settings/profile).

With the Medium version the integrator is able to create checkout pages and to integrate our communication pages as embedded pages into the integrators platform.

With the Advanced version the integrator is able use our platform completely via an API without having the buyer or seller see any DAN.COM page (with the exception of the checkout page, so we can do payment collection).

The integrator can choose which one suits best. As they all have the same endpoint (https://dan.com/api/integrator/v1), but it's up to the integrator how deep the API is used.

For example: The light integrator will only use the Orders endpoint. The medium integrator will use the Orders and Conversations endpoint, combined with embedded pages. And the Advanced Integrator will use all endpoints to completely integrate our API.

## Sign up
To get access to the API the integrator needs to create a [new account](https://dan.com/users/signup) at DAN.COM.
When all company and payout details are set you can contact support so we'll be able to upgrade that account into an integrator account.

## Authorization
In order to use the API, you need your `Integrator API Token` to generate an expiring authorization token (auth_token).
You can find your `Integrator API Token` in your [settings page](htttps://dan.com/users/settings/profile), in the section "Integrator".

So first generate an expiring authorization token:

```
$ curl -X POST -d "integrator_token=<your_api_token>" https://dan.com/api/integrator/v1/tokens
```
 
result:

```json
{
	"token":"<your_token>"
}
```

The token in the result is the authorization token which is needed for every request with the API.

You do this by passing a header along with your request.
The format of the header is `Authorization: Token <auth_token>`.
For example, if you are using `curl` you can authorize your request like this:

    $ curl -H "Authorization: Token <auth_token>" https://dan.com/api/integrator/v1/orders

If you don't provide an auth token, or your auth token is incorrect, you will receive a `401 Unauthorized` response.
Please note that the authorization token that is generated is valid for 24 hours, so it's best to not save this token but to generate a new one when setting up a session.

## API Endpoint (light)
Action | VERB | URL
--- | --- | ---
Creating an order | POST | https://dan.com/api/integrator/v1/orders
Listing all your orders | GET | https://dan.com/api/integrator/v1/orders/
Getting an order | GET | https://dan.com/api/integrator/v1/orders/<**id**>

## Orders

### Creating an order

```
POST /api/integrator/v1/orders
```

##### Example curl:

	$ curl -H "Authorization: Token :auth_token" -X POST -d "price=1000&currency_code=EUR&domain_name=testdomain.com&vat_option=vat_option_exclude&client_integrator_token=:client_integrator_token" https://dan.com/api/integrator/v1/orders/

##### Attributes

Name | Type | Description
--- | --- | ---
`client_integrator_token` | String | The token that the client copied from dan.com into your system
`price` | Integer | The price of the domain
`currency_code` | String | The currency of the price. At DAN.COM we support the following currencies as the base price: Euro (EUR), Dollar (USD) and British Pound (GBP)
`domain_name` | String | The name of the domain
`vat_option` | String | Define if the price is including or excluding vat. When the price is including vat and it appears that the buyer needs to pay vat then the vat is substracted from the price, otherwise it will be added to the price. In this example the price was excluded so the total price the buyer had to pay is the given price plus the vat. The expected values are either: 'vat_option_exclude' or 'vat_option_include'. 

##### Result

```json
{
	"order": {
		"id":1234,
		"price":1000.0,
		"company":null,
		"status":"pending_payment",
		"address1":null,
		"address2":null,
		"zip":null,
		"city":null,
		"country_code":null,
		"vat_rate":0.0,
		"vat":0.0,
		"total":1000.0,
		"currency_code":"EUR",
		"vat_option":"vat_option_exclude",
		"vat_number":null,
		"token":"29a5f8db",
		"paid_at":null,
		"created_at":"2020-07-20T11:41:33.474+02:00",
		"checkout_url":"https://dan.com/orders/29a5f8db/checkout",
		"domain_name":"testdomain.com"
	}
}
```

The checkout url is important as the integrator should communicate that one to the buyer so the buyer is able to pay for the domain

### Listing all orders

```
GET /api/integrator/v1/orders
```

##### Example curl:

	$ curl -H "Authorization: Token :auth_token" https://dan.com/api/integrator/v1/orders/

##### Result

```json
{
	"orders": [
		{
			"id":1234,
			"price":1000.0,
			"company":null,
			"status":"pending_payment",
			"address1":null,
			"address2":null,
			"zip":null,
			"city":null,
			"country_code":null,
			"vat_rate":0.0,
			"vat":0.0,
			"total":1000.0,
			"currency_code":"EUR",
			"vat_option":"vat_option_exclude",
			"vat_number":null,
			"token":"29a5f8db",
			"paid_at":null,
			"created_at":"2020-07-20T11:41:33.474+02:00",
			"checkout_url":"https://dan.com/orders/29a5f8db/checkout",
			"domain_name":"testdomain.com"
		},
		{
			# etc
		}
	]
}
```

### Getting an order

```
GET /api/integrator/v1/orders/:order_id
```

#### Example curl:
	
	$ curl -H "Authorization: Token :auth_token" https://dan.com/api/integrator/v1/orders/:order_id

#### Attributes

Name | Type | Description
--- | --- | ---
`order_id` | Integer | The id of the order you'd like to retrieve

Result

```json
{
	"order": {
		"id":1234,
		"price":1000.0,
		"company":null,
		"status":"pending_payment",
		"address1":null,
		"address2":null,
		"zip":null,
		"city":null,
		"country_code":null,
		"vat_rate":0.0,
		"vat":0.0,
		"total":1000.0,
		"currency_code":"EUR",
		"vat_option":"vat_option_exclude",
		"vat_number":null,
		"token":"29a5f8db",
		"paid_at":null,
		"created_at":"2020-07-20T11:41:33.474+02:00",
		"checkout_url":"https://dan.com/orders/29a5f8db/checkout",
		"domain_name":"testdomain.com"
	}
}
```


## API endpoint (Medium + Advanced)

### Orders (same as light integration)
Action | VERB | URL
--- | --- | ---
Creating an order | POST | https://dan.com/api/integrator/v1/orders
Listing all your orders | GET | https://dan.com/api/integrator/v1/orders/
Getting an order | GET | https://dan.com/api/integrator/v1/orders/<**id**>

### Clients
Action | VERB | URL
--- | --- | ---
Creating a client | POST | https://dan.com/api/integrator/v1/clients
Listing all your clients | GET | https://dan.com/api/integrator/v1/clients/
Showing a client | GET | https://dan.com/api/integrator/v1/clients/<**id**>
Updating a client | PUT | https://dan.com/api/integrator/v1/clients/<**id**>
Deleting a client | DELETE | https://dan.com/api/integrator/v1/clients/<**id**>

### Domains 
Action | VERB | URL
--- | --- | ---
Listing all your domains | GET | https://dan.com/api/integrator/v1/domains/
Creating a domain | POST | https://dan.com/api/integrator/v1/domains
Showing a domain | GET | https://dan.com/api/integrator/v1/domains/<**id**>
Updating a domain | PUT | https://dan.com/api/integrator/v1/domains/<**id**>
Deleting a domain | DELETE | https://dan.com/api/integrator/v1/domains/<**id**>

### Conversations
Action | VERB | URL
--- | --- | ---
Listing all Conversations | GET | https://dan.com/api/integrator/v1/conversations/
Creating a conversation | POST | https://dan.com/api/integrator/v1/conversations
Showing a conversation | GET | https://dan.com/api/integrator/v1/conversations/<**id**>
Updating a conversation | PUT | https://dan.com/api/integrator/v1/conversations/<**id**>
Deleting a conversation | DELETE | https://dan.com/api/integrator/v1/conversations/<**id**>
    
`<id>` is the unique identifier of the object you want to update/delete.

### Actions
To be defined

### Messages
To be defined

## Orders
When the client already exists on our DAN.COM and you have the DAN Distribution Network ID of that client (either the client gave it to you outside our platform or you retrieved it via the Clients API), then you can use the Orders API in the same fashion as our Light Version of the API. Do keep in mind this is only useful if the buyer and seller already agreed on a price.


## Clients
### Creating a client
```
POST https://dan.com/api/integrator/v1/clients
```

```json
{
	"client": {
      "company": "Company name",
      "address1": "Some street name 1",
      "address2": "",
      "zip": "1234 AB",
      "city": "Amsterdam",
      "country": "Nederland",
      "vat_number": "NL212121B01",
      "registration": "1234",
      "billing_name": "Billing name",
      "iban": "IBAN number",
      "phone": "12345",
      "client_type": "business" | "individual",
      "bank_code": "1234",
      "email": "some@example.com",
      "first_name": "Some",
      "last_name": "Name",
      "currency_code": "USD | GBP | EUR",
      "dan_distribution_network_id": ":dan_distrubition_network_id"
    }
}
```

### Listing all clients
To see all the clients you added to the system.

example:
```
GET /api/integrator/v1/clients/
```

```json
{
  "clients": [
    {
      "id": 123,
      "company": "Company name",
      "address1": "Some street name 1",
      "address2": "",
      "zip": "1234 AB",
      "city": "Amsterdam",
      "country": "Nederland",
      "vat_number": "NL212121B01",
      "registration": "1234",
      "billing_name": "Billing name",
      "iban": "IBAN number",
      "phone": "12345",
      "client_type": "business" | "individual",
      "bank_code": "1234",
      "email": "some@example.com",
      "first_name": "Some",
      "last_name": "Name",
      "currency_code": "USD | GBP | EUR",
      "dan_distribution_network_id": ":dan_distrubition_network_id"
    },
    #etc..
  ]
}
```


### Showing a client

```
GET https://dan.com/api/integrator/v1/clients/<id>
```
```json
{
	"client": {
	   "id": 1234,
      "company": "Company name",
      "address1": "Some street name 1",
      "address2": "",
      "zip": "1234 AB",
      "city": "Amsterdam",
      "country": "Nederland",
      "vat_number": "NL212121B01",
      "registration": "1234",
      "billing_name": "Billing name",
      "iban": "IBAN number",
      "phone": "12345",
      "client_type": "business" | "individual",
      "bank_code": "1234",
      "email": "some@example.com",
      "first_name": "Some",
      "last_name": "Name",
      "currency_code": "USD | GBP | EUR",
      "dan_distribution_network_id": ":dan_distrubition_network_id"
    }
}
```    

### Updating a client
```
PUT https://dan.com/api/integrator/v1/clients/<dan_distribution_network_id>
```
```
{
	"client": {
      "company": "Company name",
      "address1": "Some street name 1",
      "address2": "",
      "zip": "1234 AB",
      "city": "Amsterdam",
      "country": "Nederland",
      "vat_number": "NL212121B01",
      "registration": "1234",
      "billing_name": "Billing name",
      "iban": "IBAN number",
      "phone": "12345",
      "client_type": "business" | "individual",
      "bank_code": "1234",
      "email": "some@example.com",
      "first_name": "Some",
      "last_name": "Name",
      "currency_code": "USD | GBP | EUR"
    }
}
```

### Deleting a client

```
DELETE https://dan.com/api/integrator/v1/clients/<id>
```
    
##Domains

### Listing all domains
```
GET /api/integrator/v1/domains/
```

```json
{
  "domains": [
    {
      "id": 123,
      "name": "example.com",
      "stem": "example",
      "tld": "com,
      "buy_now_price": 5000,
      "starting_offer": 1000,
      "dan_distribution_network_id": 1234,
      "currency_code": "USD | GBP | EUR"
    },
    {
      "id": 124,
      "name": "other.com",
      "stem": "other",
      "tld": "com,
      "buy_now_price": 200,
      "starting_offer": 50,
      "dan_distribution_network_id": 1234,
      "currency_code": "USD | GBP | EUR"
    },
    #etc..
  ]
}
```

### Showing domains
```
GET /api/integrator/v1/domains/example.com
```

```json
{
  "domain": {
    "id": 123,
    "name": "example.com",
    "stem": "example",
    "tld": "com,
    "buy_now_price": 5000,
    "starting_offer": 1000,
    "dan_distribution_network_id": 1234,
    "currency_code": "USD | GBP | EUR"
  }
}
```

### Creating domains
```
POST /api/integrator/v1/domains/
```

```json
{
  "domain": {
    "name": "example.com",
    "buy_now_price": 5000,
    "starting_offer": 1000,
    "dan_distribution_network_id": 1234,
    "currency_code": "USD | GBP | EUR"
  }
}
```

Here is the description of each of the domain attributes:

    name: Name of your domain such as example.com (Required)
    buy_now_price: The price for which the the buyer can buy your domain (Optional)
    starting_offer: The minimum price at which buyers can bid on your domain (Optional)
    dan_distribution_network_id: The clients network id who is the owner of the domain

A sample `curl` request would be like this (JSON):

    $ curl -H "Content-Type: application/json" -X POST --data '{"domain": {"name": "example.com", "buy_now_price": 5000, "starting_offer": 1000, "dan_distribution_network_id": 1234}}' -H "Authorization: Token <your_token>" https://dan.com/api/integrator/v1/domains

Here we are passing in a JSON object which is the domain we want to create. If the domain is added successfully, you will receive a `200 OK` http response code, with a response body such as this:

```json
{
  "domain": {
    "id": 147042,
    "name": "example.com",
    "buy_now_price": 5000,
    "starting_offer": 1000,
    "dan_distribution_network_id": 1234,
    "currency_code": "USD | GBP | EUR"
  }
}
```

This is the same domain that you wanted to create. The important thing to notice here is the `id` attribute. You can use this `id` to update/delete the domain using this API (see below).

A `400 Bad Request` http response code means that the domain was not created successfully. This can happen due to several reasons, including duplicate domain name, invalid domain name (such as `example` instead of `example.com`) or invalid `buy_now_price` or `starting_offer`. The appropriate error message is returned in the response as an object with an `error` key.

For example, a request trying to create a domain with an invalid `name` returns the following response body:

```json
{
  "error": "Domain name is invalid"
}
```

### Updating domains
To update a domain, you simply need to send an http `PUT` request to

    https://dan.com/api/integrator/v1/domains/<id>

where `id` is the unique identifier of your domain. You have received the `id` of the domain when creating the domain using the API. Refer to **Creating domains** section for more information.

Just like with creating domains, you should send a `domain` object along with your request. This object should hold the new values for attributes you want to update. An example `domain` object in `JSON` would be like this:

```json
{
  "domain": {
    "name": "anothername.com",
    "buy_now_price": 10000,
    "starting_offer": 4000,
    "dan_distribution_network_id": 1234,
    "currency_code": "USD | GBP | EUR"
  }
}
```

You can update any of the domain attributes. Your update request must update at least one attribute of the domain, otherwise it will fail.

A successful update will return a `200 OK` http response code which contains the updated `domain` object in the body. Here is an example response from a domain update request:

```json
{
  "domain": {
    "id": 147042,
    "name": "anothername.com",
    "buy_now_price": 10000,
    "starting_offer": 4000,
    "dan_distribution_network_id": 1234,
    "currency_code": "USD | GBP | EUR"
  }
}
```

Note that you can not update the `id` attribute of a domain.

If the request fails to update the domain, a `400 Bad Request` http response code is returned. Just like with creating domains, the response body contains an error message describing why the update failed.

### Deleting domains
To delete a domain from your account portfolio, you need to send an http `DELETE` request to

    https://dan.com/api/integrator/v1/domains/<id>

where `id` is the unique identifier of your domain. You have received the `id` of the domain when creating the domain using the API. Refer to **Creating domains** section for more information.

An example `curl` command to delete a domain is like this:

    $ curl -X DELETE -H "Authorization: Token <your_token>" https://dan.com/api/integrator/v1/domains/147042

A `200 OK` http response code indicates that the domain has been deleted successfully. In case of any errors, you will receive a `500 Internal server error`.

## Conversations/Transactions
To have the negotiation setup our system starts out with a conversation, the moment a conversation reaches the agreement reached status an order will be created which is used for the payment collection.

### Listing all Conversations 

```
GET https://dan.com/api/integrator/v1/conversations/
```

```json
{
  "conversations": [
    {
      "id": 12,
      "client_id": 1234,
      "domain_name": "example.com",
      "name": "Buyer name",
      "phone": "Buyer phone",
      "email": "Buyer email",
      "buyer_type": "individual"
      "last_bid": "1000"
      "company_name": "",
      "currency": "USD | GBP | EUR",
      "token": ":token"
      "vat_option": "vat_option_include | vat_option_exclude",
      "embedded_seller_url": "https://dan.com/integrator/conversations/:token",
      "embedded_buyer_url": "https://dan.com/conversations/:token",
      "available_actions": {
        "seller_actions": ["decline", "counter", "accept"],
        "buyer_actions": ["revoke", "offer", "accept"]
      }
    }
  ]
}
```

### Creating a conversation

```
POST https://dan.com/api/integrator/v1/conversations
```

```json
{
  "conversation": {
  	 "client_id": 1234,
  	 "domain_name": "example.com",
  	 "name": "Buyer name",
  	 "company_name": "Company name",
  	 "buyer_type": "individual | business",
  	 "phone": "+31612345678",
  	 "email": "email@example.com",
  	 "vat_option": "vat_option_include | vat_option_exclude",
  	 "currency_code": "USD | GBP | EUR"
  }
}

```
### Showing a conversation

```
GET https://dan.com/api/integrator/v1/conversations/<id>
```

```json
{
  "conversation": {
    "id": 12,
    "client_id": 1234,
    "domain_name": "example.com",
    "name": "Buyer name",
    "phone": "Buyer phone",
    "email": "Buyer email",
    "buyer_type": "individual"
    "last_bid": "1000"
    "company_name": "",
    "currency": "USD | GBP | EUR",
    "token": ":token"
    "vat_option": "vat_option_include | vat_option_exclude",
    "embedded_seller_url": "https://dan.com/integrator/conversations/:token",
    "embedded_buyer_url": "https://dan.com/conversations/:token",
    "available_actions": {
      "seller_actions": ["decline", "counter", "accept"],
      "buyer_actions": ["revoke", "offer", "accept"]
    }
  }
}
```

### Updating a conversation

```
PUT https://dan.com/api/integrator/v1/conversations/<id>
```

```json
{
  "conversation": {
  	 "client_id": 1234,
  	 "domain_name": "example.com",
  	 "name": "Buyer name",
  	 "company_name": "Company name",
  	 "buyer_type": "individual | business",
  	 "phone": "+31612345678",
  	 "email": "email@example.com",
  	 "vat_option": "vat_option_include | vat_option_exclude",
  	 "currency_code": "USD | GBP | EUR"
  }
}
```

### Deleting a conversation

```
delete https://dan.com/api/integrator/v1/conversations/<id>
```
