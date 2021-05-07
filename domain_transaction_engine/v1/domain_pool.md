# Unused Domain Pool - V1
### API Endpoint
**Exact match**

    POST https://dan.com/dp/v1/demand/exact/<domain_name>


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

    $ curl -H "Authorization: Token <auth_token>" https://dan.com/api/integrator/v1/dp/demand/exact/{domain_name}

If you don't provide an auth token, or your auth token is incorrect, you will receive a `401 Unauthorized` response.
Please note that the authorization token that is generated is valid for 24 hours, so it's best to not save this token but to generate a new one when setting up a session.

### Exact match
Here’s how you retrieve an exact match domain when there’s a match:

```
post /api/integrator/v1/dp/demand/exact/<domain_name>
```

Currently we have three possible options from exact match queries. Domains with a price will return a so called called buy now (BUY_NOW) price, domains where the buyer can only make an offer (MAKE_OFFER) returns no price and finally domains that might be for sale (MAYBE_FOR_SALE) will also be classified as such to identify they are registered but unused. The last option is great when you want to offer our buyer brokerage service directly from your search. This service will be expanded soon with a fixed revenue share model.

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
**Lease To Own domains**

In the `monthly_values` object, the keys represent the lease period (in months), so in the response body shown below,
if the buyer choses for example 14 months as a lease period, the total monthly installment will amount to `€1729` per month 
and the markup with amount to `€158`. The `markup_percentage` represents a portion of the monthly installment which
will be put on top of a monthly total price.

```
{
   "results":[
      {
         "domain":{
            "name":"exampledomain.com",
            "options":[
               {
                  "buy_now":{
                     "url":"https://dan.com/buy-domain/exampledomain.com?integrator=c1c144940ebc4d571881754a737182cb&tld=nl",
                     "amount":{
                        "value":2200000,
                        "currency_code":"EUR",
                        "formatted":"€22,000.00"
                     }
                  }
               },
               {
                  "lease_to_own":{
                     "url":"https://dan.com/buy-domain/exampledomain.com?integrator=c1c144940ebc4d571881754a737182cb&tld=nl",
                     "amount":{
                        "max_lease_period":16,
                        "currency_code":"EUR",
                        "formatted":"€22,000.00",
                        "monthly_values":{
                           "2":{
                              "total":11000,
                              "markup_percentage":"0.0",
                              "markup":0
                           },
                           "3":{
                              "total":7334,
                              "markup_percentage":"0.0",
                              "markup":0
                           },
                           "4":{
                              "total":5500,
                              "markup_percentage":"0.0",
                              "markup":0
                           },
                           "5":{
                              "total":4400,
                              "markup_percentage":"0.0",
                              "markup":0
                           },
                           "6":{
                              "total":3667,
                              "markup_percentage":"0.0",
                              "markup":0
                           },
                           "7":{
                              "total":3143,
                              "markup_percentage":"0.0",
                              "markup":0
                           },
                           "8":{
                              "total":2750,
                              "markup_percentage":"0.0",
                              "markup":0
                           },
                           "9":{
                              "total":2445,
                              "markup_percentage":"0.0",
                              "markup":0
                           },
                           "10":{
                              "total":2200,
                              "markup_percentage":"0.0",
                              "markup":0
                           },
                           "11":{
                              "total":2000,
                              "markup_percentage":"0.0",
                              "markup":0
                           },
                           "12":{
                              "total":1834,
                              "markup_percentage":"0.0",
                              "markup":0
                           },
                           "13":{
                              "total":1862,
                              "markup_percentage":"0.1",
                              "markup":170
                           },
                           "14":{
                              "total":1729,
                              "markup_percentage":"0.1",
                              "markup":158
                           },
                           "15":{
                              "total":1614,
                              "markup_percentage":"0.1",
                              "markup":147
                           },
                           "16":{
                              "total":1513,
                              "markup_percentage":"0.1",
                              "markup":138
                           }
                        }
                     }
                  }
               }
            ]
         }
      }
   ]
}
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
            "url": "https://dan.com/buy-domain/domain_name.com",
          }
        }
      ]
    }
  }
]

The Undeveloped Domain Pool API is currently in beta stage. Any comments, requests and feedback are welcome.
