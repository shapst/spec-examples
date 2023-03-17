# REST API Error Codes

## RFC 7807

RFC-7807
This standard defines a media type application/problem+json (or +xml) and the standard fields to be used with their
exact semantics. Here's a short summary:

- **type**: a URI to identify what type of problem occurred. Ideally it should be a stable URL to the documentation of
  the details about this type of error, e.g. https://api.myshop.example/problems/not-entitled-for-payment-method; but it
  also can be a URN, e.g. urn:problem-type:not-entitled-for-payment-method. In any case, changing the type is defined to
  be a breaking API change, so it's safe for a client to use this to switch to different problem situations.
- **title**: an informal, human readable short description of the general type of problem, e.g. You're not entitled to
  use this payment method. Can be changed without breaking the API.
- **status**: repeats the response status code, e.g. 403 for Forbidden. There might be a difference between what the
  server threw and the client received due to a proxy changing the http status code. It's only advisory to help
  debugging, so it can be changed without breaking the API.
- **detail**: a human readable full description about what went wrong, e.g. Customer 123456 has only GOLD status but
  needs PLATINUM to be entitled to order the sum of USD 1,234.56 on account. can be changed without breaking the API.
- **instance**: a URI identifying the specific occurrence of the problem. If this is a URL, it should provide details
  about this occurrence, e.g. point to your
  logs https://logging.myshop.example/prod?query=d294b32b-9dda-4292-b51f-35f65b4bf64d — note that just because it's a
  URL doesn't mean it has to be accessible to everybody! If you don't even want to provide details about your logging
  system on the web, you can also produce a UUID URN like urn:uuid:d294b32b-9dda-4292-b51f-35f65b4bf64d. Can be changed
  without breaking the API.

All other fields are extensions, i.e. custom, machine readable fields; e.g. customer-status or order-sum. Extensions can
also be complex types, i.e. lists or objects containing multiple fields, as long as they are (de)serializable. The
client might want to display this to the customer. You can add new extensions without breaking the API, but removing
extensions (or changing the semantics) is a breaking API change.
NOTE: It's easy to say that the type URI needs to be stable. But it must. not. change. Even when you move your
documentation to a different host or different wiki, rename packages or class names, or even rewrite your service in a
different tech stack. And as error conditions are often not tested as thoroughly as they should be, it may even take
some time for the break to become evident.


Example of the errors of invalid input parameters:
```json
{
  "type": "https://example.net/validation-error",
  "title": "Your request parameters didn't validate.",
  "violations": [
    {
      "field": "age",
      "message": "must be a positive integer"
    },
    {
      "field": "color",
      "message": "must be 'green', 'red' or 'blue'"
    }
  ]
}
```

## Status codes

There are really only 3 outcomes in the interaction between an app and an API:

- 200x - Everything worked
- 400x - The application did something wrong
- 500x - The API did something wrong

The backend API operates the codes:

| Code | Description           | Description                                                                           |
|------|-----------------------|---------------------------------------------------------------------------------------|
| 200  | OK                    | must not be used to communicate errors in the response body                           |
| 201  | Created               | indicates that the request has succeeded and has led to the creation of a resource    |
| 400  | Bad Request           | used to indicate nonspecific failure                                                  |
| 401  | Unauthorized          | must be used when there is a problem with the client’s credentials                    |
| 403  | Forbidden             | should be used to forbid access regardless of authorization state                     |
| 404  | Not Found             | must be used when a client’s URI cannot be mapped to a resource                       |
| 500  | Internal Server Error | should be used to indicate API malfunction 500 is the generic REST API error response |
| 503  | Service Unavailable   | indicates that the server is not ready to handle the request                          |
