# Domain Transaction Engine API - V1 - Version 1.0.2

The DAN Domain Transaction API is designed specifically for registrars, domain marketplaces & other services in the domain industry that handle domain ownership transfers from one owner to another.

One of the big users and the main developer of this API is DAN.COM, which is a domain marketplace that handles over 30,000 domain ownership transfers a year.

Regardless of the size of your company or product team, we’ve got you covered.

Implementing our API is simple and straightforward. Before you proceed, first determine which integration type works best for you.

We offer three types of integration:

* Lightweight (Best option for Small companies)
* Medium (Best for Medium sized companies)
* Advanced (Best for Enterprise users that handle +$10M in Yearly sales)

**The big differences between the integration types that we offer:**

**Lightweight Integration Option:**

With the lightweight integration you can create transactions on behalf of a DAN.COM seller. This means that the seller in question has to have a DAN.COM account.

In this situation the transaction is created by the integrator (you) and DAN.COM will handle the entire transaction from A to Z for your buyers and sellers. The integrator will need the sellers `Dan Distribution Network ID`, which the seller can find in their settings page.

This model works best for integrators that want to dedicate as little as possible time on development.

**Medium Integration Option (includes Embedable pages)**:

Important note: this integration type is not yet production ready!
With our Medium integration version, you can create checkout pages but also integrate our communication pages as embedded pages into your own platform. This allows you to use our optimized pages which will be updated frequently so you can focus on growing your business while we take care of most of the tech involved to handle a transaction for you.

**Advanced Integration Option:**

This integration type works best for Enterprises that want to leverage the advantages of using our transactional engine while keeping your customers in your own customer journey. 
This integration option provides ultimate freedom of implementation for the integrator. You can even use your own PSP in this model. 
Once we’ve defined your integration scope, we will co-create a tailored approach for you and provide hands-on support to make sure you can initiate and complete the integration as easily and efficiently as possible.
**Contact our support team to start an Advanced Integration of our API.**

-------
Moving on. As an integrator you can choose which integration option suits you best. All options have the same endpoint (https://dan.com/api/integrator/v1), but it's up to the integrator how you use the API to accomplish your goals & objectives.
For example: The lightweight option will only use the Orders endpoint. The Medium option will use the Orders and Conversations endpoint, combined with embedded pages. And lastly, the Advanced option will use all endpoints relevant.




## Sign up

To get access to the API the integrator needs to create a new account at DAN.COM. When you have saved your company and payout details are set, you can contact our support team so we'll be able to upgrade that account into an integrator account.
From this account you can track your revenue, set your own commission rate and more.

## Authorization
In order to use the API, you need your `Integrator API Token` to generate an expiring authorization token (auth_token).
You can find your `Integrator API Token` in your [settings page](https://dan.com/users/settings/profile), in the section "Integrator".

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

## API Endpoint (lightweight)
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

Name | Type | Description | Required
--- | --- | --- | ---
`client_integrator_token` | String | The token that the client copied from dan.com into your system | yes
`price` | Integer | The price of the domain | yes
`currency_code` | String | The currency of the price. At DAN.COM we support the following currencies as the base price: Euro (EUR), Dollar (USD) and British Pound (GBP) | yes
`domain_name` | String | The name of the domain | yes
`vat_option` | String | Define if the price is including or excluding vat. When the price is including vat and it appears that the buyer needs to pay vat then the vat is subtracted from the price, otherwise it will be added to the price. In this example the price was excluded so the total price the buyer had to pay is the given price plus the vat. The expected values are either: `vat_option_exclude`, `vat_option_include` or `vat_option_do_not_show`. | yes
`name` | String | The buyer's full name | yes
`email` | String | The buyer's email | yes
`phone` | String | The buyer's phone number | yes
`country_code` | String | ALPHA-2 country code of the buyer. | no
`company` | String | Buyer's company name  | only if `buyer_type == "business"`
`address1` | String | The buyer's address | no
`address2` | String | The buyer's additional address information. | no
`city` | String | The buyer's city | no
`zip` | String | Zip code of the buyer. | no
`state` | String | The US state of the buyer, in Two-Letter State Abbreviations format. | only if `country_code == "US"`
`buyer_type` | String | Buyer type. `individual` or `business`. | no
`buying_action` | String | A buyer action. The expected values are either: `buy_now` or `buy_in_installments`. The default value is `buy_now` | no
`number_of_installments` | Integer | Number of installments. The value must be between `2` and `60` | only if `buying_action == "buy_in_installments"`
`vat_number` | String | VAT number of the buyer | only if `buyer_type == "business"` AND the country_code represents an EU country.

##### Result

###### Buy now

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
    "buying_action":"buy_now",
    "vat_number":null,
    "token":"29a5f8db",
    "paid_at":null,
    "created_at":"2020-07-20T11:41:33.474+02:00",
    "checkout_url":"https://dan.com/orders/29a5f8db/checkout",
    "domain_name":"testdomain.com",
  }
}
```

###### Buy in installments

```json
{
  "order": {
    "id":1234,
    "price":12000.0,
    "company":null,
    "status":"pending_payment",
    "address1":null,
    "address2":null,
    "zip":null,
    "city":null,
    "country_code":null,
    "vat_rate":0.0,
    "vat":0.0,
    "total":12000.0,
    "currency_code":"EUR",
    "vat_option":"vat_option_exclude",
    "buying_action":"buy_in_installments",
    "number_of_installments":60,
    "installment_total":200,
    "vat_number":null,
    "token":"29a5f8db",
    "paid_at":null,
    "created_at":"2020-07-20T11:41:33.474+02:00",
    "checkout_url":"https://dan.com/orders/29a5f8db/checkout",
    "domain_name":"testdomain.com",
    "number_of_installments": 12,
    "next_installment_due_at": "2020-11-20T00:00:00.000+02:00"
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
      "buying_action":"buy_now",
      "vat_number":null,
      "token":"29a5f8db",
      "buying_type""paid_at":null,
      "created_at":"2020-07-20T11:41:33.474+02:00",
      "checkout_url":"https://dan.com/orders/29a5f8db/checkout",
      "domain_name":"testdomain.com"
    },
    {
      "# etc"
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
    "buying_action":"buy_now",
    "vat_number":null,
    "token":"29a5f8db",
    "paid_at":null,
    "created_at":"2020-07-20T11:41:33.474+02:00",
    "checkout_url":"https://dan.com/orders/29a5f8db/checkout",
    "domain_name":"testdomain.com"
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
Showing a client | GET | https://dan.com/api/integrator/v1/clients/<**client_integrator_token**>
Updating a client | PUT | https://dan.com/api/integrator/v1/clients/<**client_integrator_token**>
Deleting a client | DELETE | https://dan.com/api/integrator/v1/clients/<**client_integrator_token**>

## Bank accounts
Action | VERB | URL
--- | --- | ---
Updating a client's bank account | PUT | https://dan.com/api/integrator/v1/clients/<**client_integrator_token**>/bank_accounts/<**id**>
Creating a client's bank account | PUT | https://dan.com/api/integrator/v1/clients/<**client_integrator_token**>/bank_accounts

## Paypal accounts
Action | VERB | URL
--- | --- | ---
Updating a client's paypal account | PUT | https://dan.com/api/integrator/v1/clients/<**client_integrator_token**>/paypal_accounts/<**id**>
Creating a client's paypal account | PUT | https://dan.com/api/integrator/v1/clients/<**client_integrator_token**>/paypal_accounts

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
   "email": "some@example.com",
   "first_name": "Some",
   "last_name": "Name",
   "currency_code": "USD | GBP | EUR",
   "payout_method": "payout_through_bank_transfer" | "payout_through_paypal"
   "bank_accounts": [<bank_account>],
   "paypal_accounts": [<paypal_account>],
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
      "payout_method": "payout_through_bank_transfer" | "payout_through_paypal"
      "bank_accounts": [<bank_account>],
      "paypal_accounts": [<paypal_account>],
      "dan_distribution_network_id": ":dan_distrubition_network_id"
    },
  ]
}
```


### Showing a client

```
GET https://dan.com/api/integrator/v1/clients/<id>
```
```json
{
  "client":{
    "id":1234,
    "company":"Company name",
    "address1":"Some street name 1",
    "address2":"",
    "zip":"1234 AB",
    "city":"Amsterdam",
    "country":"Nederland",
    "vat_number":"NL212121B01",
    "registration":"1234",
    "billing_name":"Billing name",
    "iban":"IBAN number",
    "phone":"12345",
    "client_type":"business""|""individual",
    "bank_code":"1234",
    "email":"some@example.com",
    "first_name":"Some",
    "last_name":"Name",
    "currency_code":"USD | GBP | EUR",
    "payout_method": "payout_through_bank_transfer" | "payout_through_paypal"
    "bank_accounts": [<bank_account>],
    "paypal_accounts": [<paypal_account>],
    "dan_distribution_network_id":":dan_distrubition_network_id"
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
    "company":"Company name",
    "address1":"Some street name 1",
    "address2":"",
    "zip":"1234 AB",
    "city":"Amsterdam",
    "country":"Nederland",
    "vat_number":"NL212121B01",
    "registration":"1234",
    "billing_name":"Billing name",
    "iban":"IBAN number",
    "phone":"12345",
    "client_type":"business""|""individual",
    "bank_code":"1234",
    "email":"some@example.com",
    "first_name":"Some",
    "last_name":"Name",
    "bank_accounts": [<bank_account>],
    "paypal_accounts": [<paypal_account>],
    "payout_method": "payout_through_bank_transfer" | "payout_through_paypal"
    "currency_code":"USD | GBP | EUR"
  
```

### Deleting a client

```
DELETE https://dan.com/api/integrator/v1/clients/<id>
```

## Bank Accounts

##### Attributes

Name | Type | Description | Required
--- | --- | --- | ---
`client_integrator_token` | String | The token that the client copied from dan.com into your system | yes
`bank_code` | String | BIC/SWIFT number  | yes
`region` | String | Bank region. Allowed values: `sepa`, `usa`, `rest_of_world` | yes
`account_holder` | Integer | Bank account holder name | yes
`bank_name` | String | Bank name | yes
`account_number` | String | Account number. if `region == 'sepa'`, IBAN format is required. | yes
`address` | String | Bank address | only if `region == 'usa'`
`zip` | String | Bank zip code | only if `region == 'usa'`
`city` | String | Bank city | only if `region == 'usa'`
`routing_number` | String | Bank's routing number. | only if `region == 'usa'`

### Creating a bank account

For now, we allow only 1 bank account per client.

```
POST /api/integrator/v1/clients/<client_integrator_token>/bank_accounts
```


```json
{
  {
   "bank_account": {
      "region":"usa",
      "bank_name":"Citi Bank",
      "id":47953,
      "bank_code":"CITIUS3PPBX",
      "account_number":"10000010043310123",
      "routing_number":"21000089",
      "address":"11 Wall Street",
      "city":"New York",
      "zip":"10013",
      "account_holder":"John Smith"
   }
}
```

### Updating a bank account

For now, we allow only 1 bank account per client.

```
PUT /api/integrator/v1/clients/<client_integrator_token>/bank_accounts/<id>
```


```json
{
  {
   "bank_account": {
      "region":"usa",
      "bank_name":"Citi Bank",
      "id":47953,
      "bank_code":"CITIUS3PPBX",
      "account_number":"10000010043310123",
      "routing_number":"21000089",
      "address":"11 Wall Street",
      "city":"New York",
      "zip":"10013",
      "account_holder":"John Smith"
   }
}
```

## Paypal Accounts

##### Attributes

Name | Type | Description | Required
--- | --- | --- | ---
`client_integrator_token` | String | The token that the client copied from dan.com into your system | yes
`email` | String | Paypal email  | yes

### Creating a Paypal account

For now, we allow only 1 bank account per client.

```
POST /api/integrator/v1/clients/<client_integrator_token>/paypal_accounts
```

```json
{
   "paypal_account": {
      "email":"example@example.com",
   }
}
```

### Updating a bank account

For now, we allow only 1 bank account per client.

```
PUT /api/integrator/v1/clients/<client_integrator_token>/paypal_accounts/<id>
```

```json
{
   "paypal_account": {
      "email":"example@example.com",
   }
}
```

    
### Domains

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
      "tld": "com",
      "buy_now_price": 5000,
      "starting_offer": 1000,
      "dan_distribution_network_id": 1234,
      "currency_code": "USD | GBP | EUR"
    },
    {
      "id": 124,
      "name": "other.com",
      "stem": "other",
      "tld": "com",
      "buy_now_price": 200,
      "starting_offer": 50,
      "dan_distribution_network_id": 1234,
      "currency_code": "USD | GBP | EUR"
    },
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
    "tld": "com",
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
To have the negotiation setup our system starts out with a conversation, the moment an agreement is reached the conversation will get an "agreement_reached" status and order will be created which is used for the payment collection.

### links

It loosely follows HATEOAS pattern: [https://en.wikipedia.org/wiki/HATEOAS]() and lists all the available actions (either for saller or buyer) at a certain conversation/transaction state.
The structure of ```links``` is following:

```json
{
  "links": {
    "buyer_actions": {
      "ACTION_NAME": {
        "method": "HTTP_METHOD",
        "link": "RESOURCE_URL",
        "params": "AN OBJECT WITH PARAMS DEFINITIONS",
      }
    },
    "seller_actions": {
      "method": "HTTP_METHOD",
      "link": "RESOURCE_URL",
      "params": "AN OBJECT WITH PARAMS DEFINITIONS",
    }
  }
}
```

#### Params definitions

The ```params``` key contains information about the parameter names and their data types, for example:

```
  bid: "integer" - it means that the system expects a bid parameter of a type integer
  domain_transfer_method: ["transfer_via_push", "transfer_via_auth_code"] - the system expects one of the values listed within the array (mutually exclusive)
  
```

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
      "buyer_type": "individual",
      "last_bid": "1000",
      "company_name": "",
      "currency": "USD | GBP | EUR",
      "token": ":token",
      "conversation_state": "counter_offer",
      "vat_option": "vat_option_include | vat_option_exclude",
      "embedded_seller_url": "https://dan.com/integrator/conversations/:token",
      "embedded_buyer_url": "https://dan.com/conversations/:token",
      "links": {
        "buyer_actions": {
          "reject": {
            "method": "put",
            "href": "https://dan.com/conversations/:token/reject",
            "params": {},
          },
          "offer": {
            "method": "put",
            "href": "https://dan.com/conversations/:token/offer",
            "params": {"bid": "integer"}
          },
          "accept": {
            "method": "put",
            "href": "https://dan.com/conversations/:token/accept",
            "params": {}
          }
        },
        "seller_actions": {
          "revoke": {
            "method": "put",
            "href": "https://dan.com/conversations/:token/revoke",
            "params": {}
          },
          "counter": {
            "method": "put",
            "href": "https://dan.com/conversations/:token/offer",
            "params": {"bid": "integer"}
          },
          "accept": {
            "method": "put",
            "href": "https://dan.com/conversations/:token/accept",
            "params": {}
          }
        } 
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
  "conversation":{
    "id":12,
    "client_id":1234,
    "domain_name":"example.com",
    "name":"Buyer name",
    "phone":"Buyer phone",
    "email":"Buyer email",
    "buyer_type":"individual",
    "last_bid":"1000",
    "company_name":"",
    "currency":"USD | GBP | EUR",
    "token":":token",
    "conversation_state":"counter_offer",
    "vat_option":"vat_option_include | vat_option_exclude",
    "embedded_seller_url":"https://dan.com/integrator/conversations/:token",
    "embedded_buyer_url":"https://dan.com/conversations/:token",
    "links":{
      "buyer_actions":{
        "reject":{
          "method":"put",
          "href":"https://dan.com/conversations/:token/reject",
          "params":{
            
          }
        },
        "offer":{
          "method":"put",
          "href":"https://dan.com/conversations/:token/offer",
          "params":{
            "bid":"integer"
          }
        },
        "accept":{
          "method":"put",
          "href":"https://dan.com/conversations/:token/accept",
          "params":{
            
          }
        }
      },
      "seller_actions":{
        "revoke":{
          "method":"put",
          "href":"https://dan.com/conversations/:token/revoke",
          "params":{
            
          }
        },
        "counter":{
          "method":"put",
          "href":"https://dan.com/conversations/:token/offer",
          "params":{
            "bid":"integer"
          }
        },
        "accept":{
          "method":"put",
          "href":"https://dan.com/conversations/:token/accept",
          "params":{
            
          }
        }
      }
    }
  }
}

```
### Showing a conversation

```
GET https://dan.com/api/integrator/v1/conversations/<id>
```

#### Conversation in "payment_received" state

```json
{
  "conversation": {
    "id":12,
    "client_id":1234,
    "domain_name":"example.com",
    "name":"Buyer name",
    "phone":"Buyer phone",
    "email":"Buyer email",
    "buyer_type":"individual",
    "last_bid":"1000",
    "company_name":"",
    "currency":"USD | GBP | EUR",
    "token":":token",
    "conversation_state":"domain_transfer_initiated",
    "vat_option":"vat_option_include | vat_option_exclude",
    "embedded_seller_url":"https://dan.com/integrator/conversations/:token",
    "embedded_buyer_url":"https://dan.com/conversations/:token",
    "links":{
      "buyer_actions":{
        "message":{
          "method":"post",
          "href":"https://dan.com/conversations/:token/message",
          "params":{
            "description":"string"
          }
        }
      },
      "seller_actions":{
        "choose_domain_transfer_method":{
          "method":"put",
          "href":"https://dan.com/conversations/:token/choose_domain_transfer",
          "params":{}
        }
      }
    }
  }
}
```

### Webhooks:

If the webhook url in the integrator's seller profile is set, every transaction state change will result in a webhook being sent via POST HTTP method.

The payload will consist of an event name representing the conversation state change, conversation_token and and metadata.

If a webhook endpoint responds with status code 200, the webhook will be considered as delivered. Otherwise, the system will try again to deliver it in 15 minutes (max 5 retries).

List of available webhooks with example payloads:

```json
offer (when a buyer sends an offer for a domain as a response to a counter_ofer):
{"user_email":"example@example.com","seller_dan_distribution_network_id":"e71d16e2fb9c12cf12321cd2c09d4357","message_type":"buyer","domain_id":2940,"domain_name":"exampledomain.com","conversation_token":"05779a447bdd863bd42dd9d430edf35d","bid":1250,"currency_code":"EUR","request_id":"90ddd7e3-5a38-4053-89c3-191cb394b4a9","event_type":"offer","utc_timestamp":1604576818}
counter_offer (when a seller sends a counter offer as an response to the buyer's offer):
{"user_email":"antonylubowitz@langosh.net","seller_dan_distribution_network_id":"e71d16e2fb9c12cf12321cd2c09d4357","message_type":"domainer","domain_id":2940,"domain_name":"exampledomain.com","conversation_token":"05779a447bdd863bd42dd9d430edf35d","bid":1300,"currency_code":"EUR","request_id":"bc861abe-b140-4fd1-a01a-b9760f4f3364","event_type":"counter_offer","utc_timestamp":1604576815}
revoke (when the seller revokes his/her counter offer):
{"user_email":"antonylubowitz@langosh.net","seller_dan_distribution_network_id":"e71d16e2fb9c12cf12321cd2c09d4357","message_type":"domainer","domain_id":2940,"domain_name":"exampledomain.com","conversation_token":"05779a447bdd863bd42dd9d430edf35d","request_id":"944fc26c-2b38-443d-a908-51a9eaf15192","event_type":"revoke","utc_timestamp":1604576816}
accept (when seller or a buyer accepts an offer or a counter offer):
{"user_email":"example@example.com","seller_dan_distribution_network_id":"e71d16e2fb9c12cf12321cd2c09d4357","message_type":"buyer","domain_id":2940,"domain_name":"exampledomain.com","conversation_token":"05779a447bdd863bd42dd9d430edf35d","request_id":"a236b3b0-a6f0-49e5-bb5a-d74c82caf649","event_type":"accept","utc_timestamp":1604576820}
payment_received (when DAN.com received a payment for a domain with buy_now option or receives a payment for a 1st installment):
{"user_email":"example@example.com","seller_dan_distribution_network_id":"e71d16e2fb9c12cf12321cd2c09d4357","message_type":"system","domain_id":2940,"domain_name":"exampledomain.com","conversation_token":"05779a447bdd863bd42dd9d430edf35d","request_id":"19cc1208-8635-4001-8b86-b9319a0b3f56","event_type":"payment_received","utc_timestamp":1604576849}
choose_domain_transfer (when a seller chooses an internal domain push after us receiving a payment):
{"user_email":"dan@example.com","seller_dan_distribution_network_id":"ba09c1f07da70c868d92ea864f87925a","message_type":"domainer","domain_id":2941,"domain_name":"exampledomain.com","conversation_token":"eaf3d3cd2b3d37241a52bc9d23a67f2a","request_id":"64491c9a-f1c6-49f0-87b5-2fb740e023b6","event_type":"choose_domain_transfer","utc_timestamp":1607511600}
choose_domain_push (when seller chooses an internal domain push from among the domain transfer options):
{"user_email":"antonylubowitz@langosh.net","seller_dan_distribution_network_id":"e71d16e2fb9c12cf12321cd2c09d4357","message_type":"domainer","domain_id":2940,"domain_name":"exampledomain.com","conversation_token":"05779a447bdd863bd42dd9d430edf35d","request_id":"4d3e4e3d-5889-4252-b99b-e87936d46d8a","event_type":"choose_domain_push","utc_timestamp":1604576851}
seller_asked_to_push_domain (when sellers chooses a domain transfer via an internal push, please see the "instructions" property):
{"user_type":"system","domain_id":4808,"domain_name":"exampledomain.com","seller_dan_distribution_network_id":"afb869057a74b6d1c3adc822686b0914","message":"Can you please push the domain to our escrow account at Godaddy?\n\nYou can use the following account to push the domain to:\n\nEmail: **escrow@undeveloped.com**\n\nCustomer account number: **115847677**\n\n**Important:** Please select (Yes) while pushing the domain to copy the current domain information to the new account. We’ll edit the contact details of importance ourselves when accepting the domain in our account at Godaddy.\n","instructions":{"registrar":"godaddy_com","target_account":{"email":"escrow@undeveloped.com","handle":115847677}},"request_id":"a66a8135-dc39-471b-8380-a3bab807df6e","event_type":"seller_asked_to_push_domain","utc_timestamp":1604929579}
instruct_buyer_domain_transfer (after seller aknowledges an internal domain push):
{"user_email":"example@example.com","seller_dan_distribution_network_id":"afb869057a74b6d1c3adc822686b0914","message_type":"system","domain_id":4808,"domain_name":"exampledomain.com","conversation_token":"be247dc37b66484109330ae91f52eb1f","description":"The domain **%{domain_name}** that you’ve acquired is currently registered and managed at the domain registrar Godaddy.com.\n\nTo be able to deliver this domain to you fastest, please follow the next two simple steps:\n\n1: **Please create a Godaddy.com account** or **skip this step if you already have one.**\n\n2: Send us **the email address** and **customer account number** # assigned to your account by Godaddy.\n\nWithin one business day after receiving the requested above details, we’ll transfer the domain to your account so you can start managing and using your domain.\n\n**Good to know:** Each domain registrar has different processes and specific lock-ins. If you plan to keep the domain at Godaddy, please update the ownership details of the domain to reflect your details. Otherwise, the seller will receive essential emails from Godaddy instead of you.\n\nOr\n\nIf you plan to transfer the domain to another registrar, first check when Godaddy will allow you to transfer the domain to another registrar and only update the administrative records of the domain to reflect your email address. Once the domain is transferred to your selected registrar, immediately update the full registrant records, and you’re all set!\n","registrar":"godaddy_com","request_id":"d456b2f4-70cc-45c9-8dd0-90247a599323","event_type":"instruct_buyer_domain_transfer","utc_timestamp":1604929580}
domain_has_transferlock:
{"user_type":"system","domain_id":4801,"domain_name":"exampledomain.com","seller_dan_distribution_network_id":"244ec8ba8992ddbf66d446531f6df9da","request_id":"f8df85ee-bdc5-4f12-958d-b698892fce5a","event_type":"domain_has_transferlock","utc_timestamp":1604397600}
account_change_initiated (this means the transfer process has been already initiated by the seller):
{"user_email":"chet@brekke.name","seller_dan_distribution_network_id":"afb869057a74b6d1c3adc822686b0914","message_type":"admin","domain_id":4808,"domain_name":"exampledomain.com","conversation_token":"be247dc37b66484109330ae91f52eb1f","description":"Thank you. I've just initiated the account change at Godaddy.\n\nPlease note that domains transfers at Godaddy can get locked up relatively easy due to their processes. Please follow my instructions below very carefully to avoid delays:\n\n**Step 1:**\n\nFirst, you have to accept the domain transfer via the email Godaddy has sent you to the email address you provided us earlier.\n\n**Step 2:**\n\nAt this step, you have to accept the domain transfer in your Domain Manager at Godaddy. Since this domain is already registered at Godaddy, you have to visit the \"Pending account change\" section to accept the domain transfer. You can also directly head to this url if you can't find the \"Pending account changes\" section: https://dcc.godaddy.com/transfers/in\n\n**Step 3:**\n\nWhen accepting the domain, Godaddy will show you the contact details of the domain. Do not change or update anything at this step yet! First complete the transfer, once that's done edit the ownership records.\n\nYou can also read here how to accept incoming account changes at Godaddy: https://www.godaddy.com/help/accept-a-domain-name-account-change-1670\n\nPlease confirm once you've completed the above action.\n","registrar":"godaddy_com","request_id":"a845c3be-4e26-4e80-bea0-8e2f17de40c3","event_type":"account_change_initiated","utc_timestamp":1604929581}
domain_transferred (when DAN.com confirms the domains is suffessfuly transferred):
{"user_email":"tamera@rutherfordemmerich.co","seller_dan_distribution_network_id":"e71d16e2fb9c12cf12321cd2c09d4357","message_type":"admin","domain_id":2940,"domain_name":"exampledomain.com","conversation_token":"05779a447bdd863bd42dd9d430edf35d","request_id":"911b6142-2153-4f05-af4b-08597d27379c","event_type":"domain_transferred","utc_timestamp":1604576853}
payment_reminder:
{"user_email":"example@email.com","seller_dan_distribution_network_id":"ba09c1f07da70c868d92ea864f87925a","message_type":"system","domain_id":2941,"domain_name":"exampledomain.com","conversation_token":"eaf3d3cd2b3d37241a52bc9d23a67f2a","request_id":"56d582c1-9533-4ec3-8044-059f21768ee7","event_type":"payment_reminder","utc_timestamp":1604483100}
auth_code_sent (when we sent an authcode to a buyer):
{"user_type":"system","domain_id":2941,"domain_name":"exampledomain.com","seller_dan_distribution_network_id":"ba09c1f07da70c868d92ea864f87925a","request_id":"8834695f-915e-44e3-8f97-355508a3e9ba","event_type":"auth_code_sent","utc_timestamp":1607684400}
submit_auth_code (when a seller submits an authcode):
{"user_email":"dan@example.com","seller_dan_distribution_network_id":"ba09c1f07da70c868d92ea864f87925a","message_type":"domainer","domain_id":2941,"domain_name":"exampledomain.com","conversation_token":"eaf3d3cd2b3d37241a52bc9d23a67f2a","auth_code":"1231231","request_id":"60bfa93b-1add-46ef-9b05-df4679f2c06f","event_type":"submit_auth_code","utc_timestamp":1607684400}
installment_will_be_charged (announcment, that an installment payment will be charged soon):
{"seller_dan_distribution_network_id":"244ec8ba8992ddbf66d446531f6df9da","installment":2,"order_id":1482,"conversation_token":"20d56c77d5f3e1f9b314c1c4115ad7bb","date":1606950000,"request_id":"e0d6d63f-c1d0-4ba5-8332-db9c2b6b234f","event_type":"installment_will_be_charged","utc_timestamp":1606730400}
installment_payment_failure (error message baing a result of a installment payment failure):
{"seller_dan_distribution_network_id":"5b8daf28cd748f01d96250587b7c5961","installment":2,"order_id":820,"conversation_token":"24ac0bfacf5e2273e794575737ae114c","request_id":"9a68c02c-97ba-4d52-8a4c-6a2e82393fb3","event_type":"installment_payment_failure","utc_timestamp":1607166000}
installment_payment_reminder:
{"seller_dan_distribution_network_id":"5b8daf28cd748f01d96250587b7c5961","installment":2,"order_id":820,"conversation_token":"24ac0bfacf5e2273e794575737ae114c","request_id":"b9735bfa-d43e-4220-95d6-c0a5fd4299cb","event_type":"installment_payment_reminder","utc_timestamp":1607511600}
installment_payment_reminder_final:
{"seller_dan_distribution_network_id":"5b8daf28cd748f01d96250587b7c5961","installment":2,"order_id":820,"conversation_token":"24ac0bfacf5e2273e794575737ae114c","request_id":"9108e264-2617-4be6-b54c-acb5609898ee","event_type":"installment_payment_reminder_final","utc_timestamp":1607684400}
installment_buyer_in_default:
{"seller_dan_distribution_network_id":"5b8daf28cd748f01d96250587b7c5961","installment":2,"order_id":820,"conversation_token":"24ac0bfacf5e2273e794575737ae114c","request_id":"89f64c86-7935-4fad-bbe0-8dd9af0800ec","event_type":"installment_buyer_in_default","utc_timestamp":1607857200}
transaction_cancelled:
{"user_email":"ethanwilderman@huel.biz","seller_dan_distribution_network_id":"5b8daf28cd748f01d96250587b7c5961","message_type":"admin","domain_id":2942,"domain_name":"exampledomain.com","conversation_token":"24ac0bfacf5e2273e794575737ae114c","request_id":"9e357c70-c02b-496c-8dfb-476ff2ba14cc","event_type":"transaction_cancelled","utc_timestamp":1604576903}
```

Example:

```json
{
  "id": "98d3ade3-c74f-4b4c-9fa5-23f400cd1e3d",
  "event_type": "domain_name_transferred",
  "conversation_token": "a7003aa7e2c72d142ef0533dcc232294",
  "metadata": {}
}
```


### Error messages:

Currently we return following resource-related error messages:

```
missing
invalid
already_exists
must_greater_than_or_equal_to_%{count}
must_be_less_than_%{count}
not_a_number
must_be_accepted
doesnt_match_%{attribute}
must_be_even
is_reserved
must_be_greater_than_%{count}
must_be_less_than_or_equal_to_%{count}
not_an_integer
must_be_odd
must_be_other_than_%{count}
must_be_blank
too_long_max_%{count}
too_short_min_%{count}
wrong_length_must_be_%{count}
```

The %{count} means the value assigned to the the error key can be dynamic.
So for example, when creating an order using ```orders``` endpoint with params: `{price: 50}`, you will encounter a following error response:

```json
{
 "message": {
   "price": ["must_greater_than_or_equal_to_100"]
 }
}
```
