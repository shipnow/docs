ShipNow API
===========

ShipNow exposes it's information using RESTful interface. Detailed
documentation of every endpoint is available [here][swagger].

Making requests
---------------

 * Our production API is hosted at https://api.shipnow.com.ar.
 * Our testing API is hosted at https://api-staging.shipnow.com.ar.

If you need to test your clients, feel free to pollute the testing API with
test data, though keep in mind that it will be cleared every few hours.

On occasions, testing may diverge slightly from production. This should not
last long, since we generally deploy to one and quickly to the other. If you
find such discrepancies, don't worry, since they should quickly propagate to
production.

Both APIs use HTTPS (ie: HTTP + TLS) exclusively. You MUST NOT attempt to reach
them using raw HTTP.

Authentication
--------------

Authentication is not yet done via OAuth, but a simpler password-based
mechanism.

Users will provide their username and password, which you will then exchange
for a `Token` by `POST`ing to `/user/authentication`, using HTTP Basic
Authentication (as defined in [rfc2617][rfc2617]). This will return the entire
user object, which contains the user's token:

    {
        "id": 1,
        "email": "hugo@barrera.io",
        "token": "VXyuYdTflB9NY1KUMfcddDcAJeye4vfAv7cZg8m7qDNmhuZ3V6",
        "type": "human",
        "description": "Hugo",
        "role": "admin",
        "account": {},
        "avatar_url": null
    }

This token should be included in every request header to operate on behalf of
the user, inside the `Authorization` header as follows:

    Token token=gNGQNzZ2muAdRKZT3miNccnwYxSrZ6zl2t1B6l6tcHpUw00pok

Accounts vs Users
-----------------

Each client has a single account, which in turn, has multiple users. An account
will hold all orders and products for that client, which all users of that
account can access (with different access levels).

Each account can have as many users as they need.

Access Levels
-------------

Users can have three access levels:

 * ShipNow: These are internal account, that can access data for all clients,
   and are used internally when actually moving products around.
   You will generally never see these user types unless you're deploying
   the ShipNow app for your own warehouse.
 * Administrator: These are the top-level users for each account, and can
   create other users, as well as a few more special actions.
 * Regular: These are the lower-level users, which can create/edit orders,
   product, and use other day-to-day interfaces.
   Bot accounts should generally also be regular users.

Data
----

All data is sent and received using [JSON][json]. Request data will be
contained inside a `results` object:

    {
        "results": [
           {
              "id": 1,
              "name": "sample object 1",
              "created_at": "2017-12-14T15:42:12.898Z"
           }
        ]
    }

In case of a 4XX or 5XX status code, errors MAY be included in an errors array:

    {
        "errors": [
            "Access Denied"
        ]
    }

We adhere quite closely to [HTTP 1.1][http1.1], with one exception: When data
validation fails, we will return a 422 status code.

About this documentation
------------------------

This documentation is hosted publicly at [GitHub][github]. If you find any
error or room for improvement, feel free to send a contribution.

Copyright (c) 2016 ShipNow SRL <developers@shipnow.com.ar>

[swagger]: https://docs.shipnow.com.ar/swagger/
[json]: http://www.ecma-international.org/publications/files/ECMA-ST/ECMA-404.pdf
[http1.1]: https://tools.ietf.org/html/rfc2616
[github]: https://github.com/shipnow/docs
[rfc2617]: https://tools.ietf.org/html/rfc2617#section-2
