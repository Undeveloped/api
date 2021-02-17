# Domain Pool API - V1 - Demand
### API Endpoint
**Exact match**

    POST https://dan.com/dp/v1/demand/exact/<domain_name>


### Authorization
In order to perform a successful API request you always have to include your authorization token (Auth Token) in the header. The format of the header is designed as follow: Authorization: Token ``<your_token>``, where ``<your_token>`` is your Auth Token.

For example, if you are using `curl` you can authorize your request like this:

    $ curl -H "Authorization: Token <your_token>" https://dan.com/dp/v1/demand/exact/{domain_name}

If you don't provide an auth token, or your auth token is incorrect, you will receive a `401 Unauthorized` http response code.

### Exact match
Here’s how you retrieve an exact match from the Domain Pool.

```
post /dp/v1/demand/exact/<domain_name>
```

Currently we have three possible options from exact match queries. Domains with a price will return a so called called buy now (BUY_NOW) price, domains where the buyer can only make an offer (MAKE_OFFER) returns no price and finally domains that might be for sale (MAYBE_FOR_SALE) will also be classified as such to identify they are registered but unused.

**Buy now domains**
```
"results": [
  {
    "domain": {
      "name": "domain_name.com",
      "options": [
        {
          "buy_now": {
            "url": "https://dan.com/buy-domain/.com/domain_name.com",
            "amount": {
              "value": 20000,
              "currency_code": "EUR",
              "formatted": "€200"
            }
          }
        }
      ]
    }
  }
]
```
**Make Offer Domains**
```
"results": [
  {
    "domain": {
      "name": "domain_name.com",
      "options": [
        {
          "make_offer": {
            "url": "https://dan.com/buy-domain/.com/domain_name.com",
          }
        }
      ]
    }
  }
]
```
**Maybe For Sale Domains**
```
"results": [
  {
    "domain": {
      "name": "domain_name.com",
      "options": [
        {
          "maybe_for_sale": {
            "url": "https://dan.com/search/com/domain_name.com"
          }
        }
      ]
    }
  }
]
```

The Undeveloped Domain Pool API is currently in beta stage. Any comments, requests and feedback are welcome.
