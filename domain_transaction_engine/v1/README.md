# API's

[Domain Pool API](https://github.com/Undeveloped/api/blob/master/domain_transaction_engine/v1/domain_pool.md)

[Transaction API](https://github.com/Undeveloped/api/blob/master/domain_transaction_engine/v1/transaction_api.md)

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
