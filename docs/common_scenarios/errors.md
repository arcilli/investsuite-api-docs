---
title: A closer look at API errors
---

Errors happen. If that is the case the API returns a `4**` error if it assumes that the error is caused by your application, or a `5**` in case of a server error. If there is no error anticipated the API returns a 2** code.

In general, `4**` errors are not retryable. `5**` errors should be escalated to InvestSuite.

### Error codes

Code | Status | Description
--- | --- | ---
200 | Successful response | The request was successfully completed.
201 | Created | A new resource was successfully created.
400 | Bad Request | The request was invalid.
401 | Unauthorized | The request did not include an authentication token or the authentication token was expired.
403 | Forbidden | The client did not have permission to access the requested resource.
404 | Not Found | The requested resource was not found.
409 | Business Logic Error | There is a conflict in the request, or the request would create a conflict with the resource
422 | Validation error | The request was understood but a validation error prevented the server to process the request. If this is the case the API adds context to the response body (see example below).
500 | Internal Server Error | The request was not completed due to an internal error on the server side.


### Example of a 400 Bad Request

When a validation error occurs the reason and the location are documented in the response body.

```json
{
    "title": "Bad request",
    "detail": [
        {
            "loc": [
                "body",
                "rik_profile_id"
            ],
            "msg": "extra fields not permitted",
            "type": "value_error.extra"
        }
    ],
    "status": 400,
    "body": {
        "rik_profile_id": "K01FJCEA3TBBXNPP6GYZ2688EE3"
    }
}
```

### Example of a 401 Unauthorised response

```json
{
    "title": "Unauthorized",
    "description": "JWT invalid",
    "request_id": "8d3cc569-00e1-9cdf-96b3-ed394f402fd3",
    "timestamp": "2021-10-19T15:06:19.855057+00:00"
}
```
