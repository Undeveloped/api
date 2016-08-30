# Domains API - V1
### API Endpoint
**Showing a domain**

    GET https://undeveloped.com/api/v1/domains/<id>

**Creating a domain**

    POST https://undeveloped.com/api/v1/domains

**Updating a domain**

    PUT https://undeveloped.com/api/v1/domains/<id>

**Deleting a domain**

    DELETE https://undeveloped.com/api/v1/domains/<id>

`<id>` is the unique identifier of the domain you want to update/delete.
This could either be our internal id or the name of the domain (ie: `example.com`)

### Authorization
In order to use the API, you need your authorization token (auth token).  
To authorize your API access, you need to pass in the auth token with each request you make. You can do this by passing a header along with your request. The format of the header is `Authorization: Token <your_token>`, where `your_token` is your auth token.  
For example, if you are using `curl` you can authorize your request like this:

    $ curl -H "Authorization: Token <your_token>" https://undeveloped.com/api/v1/domains

If you don't provide an auth token, or your auth token is incorrect, you will receive a `401 Unauthorized` http response code.
### Showing domains
If you already have a domain in our system and you want to see which information is currently in your portfolio.

example:
```
get /api/v1/domains/example.com
```
```
{
  "domain": {
    "id": 123,
    "name": "example.com",
    "buy_now_price": 5000,
    "starting_offer": 1000
  }
}
```

### Creating domains
In order to create a domain and add it to your account portfolio, you need to send an http `POST` request to the API endpoint.  
The request should contain a `domain` object which contains the attributes for your new domain. For example, here is a domain in JSON format:

    {
        "domain": {
            "name": "example.com",
            "buy_now_price": 5000,
            "starting_offer": 1000
        }
    }

Here is the description of each of the domain attributes:

    name: Name of your domain such as example.com (Required)
    buy_now_price: The price for which the the buyer can buy your domain (Optional)
    starting_offer: The minimum price at which buyers can bid on your domain (Optional)

A sample `curl` request would be like this (JSON):

    $ curl -H "Content-Type: application/json" -X POST --data '{"domain": {"name": "example.com", "buy_now_price": 5000, "starting_offer": 1000}}' -H "Authorization: Token <your_token>" https://undeveloped.com/api/v1/domains

Here we are passing in a JSON object which is the domain we want to create. If the domain is added successfully, you will receive a `200 OK` http response code, with a response body such as this:

    {
        "domain": {
            "id": 147042,
            "name": "example.com",
            "buy_now_price": 5000,
            "starting_offer": 1000
        }
    }

This is the same domain that you wanted to create. The important thing to notice here is the `id` attribute. You can use this `id` to update/delete the domain using this API (see below).

A `400 Bad Request` http response code means that the domain was not created successfully. This can happen due to several reasons, including duplicate domain name, invalid domain name (such as `example` instead of `example.com`) or invalid `buy_now_price` or `starting_offer`. The appropriate error message is returned in the response as an object with an `error` key.

For example, a request trying to create a domain with an invalid `name` returns the following response body:

    {
        "error": "Domain name is invalid"
    }


**NOTE: Your request must contain your authorization token in the request header. See the authorization section above for more information.**

### Updating domains
To update a domain, you simply need to send an http `PUT` request to

    https://undeveloped.com/api/v1/domains/<id>

where `id` is the unique identifier of your domain. You have received the `id` of the domain when creating the domain using the API. Refer to **Creating domains** section for more information.

Just like with creating domains, you should send a `domain` object along with your request. This object should hold the new values for attributes you want to update. An example `domain` object in `JSON` would be like this:


    {
        "domain": {
            "name": "anothername.com",
            "buy_now_price": 10000,
            "starting_offer": 4000
        }
    }

You can update any of the domain attributes. Your update request must update at least one attribute of the domain, otherwise it will fail.

A successful update will return a `200 OK` http response code which contains the updated `domain` object in the body. Here is an example response from a domain update request:

    {
        "domain": {
            "id": 147042,
            "name": "anothername.com",
            "buy_now_price": 10000,
            "starting_offer": 4000
        }
    }

Note that you can not update the `id` attribute of a domain.

If the request fails to update the domain, a `400 Bad Request` http response code is returned. Just like with creating domains, the response body contains an error message describing why the update failed.

**NOTE: Your request must contain your authorization token in the request header. See the authorization section above for more information.**

### Deleting domains
To delete a domain from your account portfolio, you need to send an http `DELETE` request to

    https://undeveloped.com/api/v1/domains/<id>

where `id` is the unique identifier of your domain. You have received the `id` of the domain when creating the domain using the API. Refer to **Creating domains** section for more information.

An example `curl` command to delete a domain is like this:

    $ curl -X DELETE -H "Authorization: Token <your_token>" https://undeveloped.com/api/v1/domains/147042

A `200 OK` http response code indicates that the domain has been deleted successfully. In case of any errors, you will receive a `500 Internal server error`.

**NOTE: Your request must contain your authorization token in the request header. See the authorization section above for more information.**
