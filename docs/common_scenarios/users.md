---
title: Create and manage users
---


## Create a user

=== "Request"

    ```HTTP hl_lines="1"
    POST /users/ HTTP/1.1
    Host: api.sandbox.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json
    Authorization: Bearer {string}

    {
        "external_id": "unique_external_entity_id",
        "first_name": "Ashok",
        "last_name": "Kumar",
        "email": "ashok.kumar@example.com",
        "phone": "+12345667",
        "counter_account": {
            bank_account_number: "BE01234567891234",
            bank_account_type: "IBAN",
            bank_id: "IDQMIE2D"
        },
        "language": "en-US"
    }

    ```

=== "Response (body)"

    ```JSON hl_lines="10"
    {
        "external_id": "unique_external_entity_id",
        "id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "creation_datetime": "2021-02-18T08:21:02+00:00",
        "version": 3,
        "version_datetime": "2021-02-18T08:21:02+00:00",
        "version_authored_by_user_id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "deleted": false,
        "first_name": "Jane",
        "last_name": "Doe",
        "email": "jane.doe@example.com",
        "phone": "+123456789",
        "language": "en-US"
    }
    ```

## Get a user

Add the InvestSuite ID to the path to retrieve a user object.

=== "Request"

    ```HTTP hl_lines="1"
    GET /users/U01F8YW5NJXMF78PMFKXTE2R7Q7 HTTP/1.1
    Host: api.sandbox.investsuite.com
    Authorization: Bearer {string}
    ```

=== "Response (body)"

    ```JSON hl_lines="10"
    {
        "external_id": "unique_external_entity_id",
        "readable_by": [
            "U01ARZ3NDEKTSV4RRFFQ69G5FAV"
        ],
        "modifiable_by": [
            "U01ARZ3NDEKTSV4RRFFQ69G5FAV"
        ],
        "id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "creation_datetime": "2021-02-18T08:21:02+00:00",
        "version": 3,
        "version_datetime": "2021-02-18T08:21:02+00:00",
        "version_authored_by_user_id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "deleted": false,
        "_embedded": {},
        "first_name": "Jane",
        "last_name": "Doe",
        "email": "jane.doe@example.com",
        "phone": "+123456789",
        "brokerage_user_id": "12345678",
        "idp_user_id": "user_idp_identity",
        "language": "en-US"
    }
    ```
## Change ("patch") fields

Given the right permissions you can update any object issuing a `PATCH` request. 

!!! Info
    Objects in the InvestSuite system are immutable. Every change leads to a new version so that a log exists of who performed which change at which moment. The version number is returned alongside other metadata fields. Use the Admin Console to access this log and to view diffs between versions.

Screenshot Admin Console

=== "Request"

    ```HTTP hl_lines="1"
    PATCH /users/U01F8YW5NJXMF78PMFKXTE2R7Q7 HTTP/1.1
    Host: api.sandbox.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json
    Authorization: Bearer {string}

    {
        "language": "be-NL"
    }

    ```

=== "Response (body)"

    ```JSON hl_lines="10"
    {
        "external_id": "unique_external_entity_id",
        "id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "creation_datetime": "2021-02-18T08:21:02+00:00",
        "version": 3,
        "version_datetime": "2021-02-18T08:21:02+00:00",
        "version_authored_by_user_id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "deleted": false,
        "first_name": "Jane",
        "last_name": "Doe",
        "email": "jane.doe@example.com",
        "phone": "+123456789",
        "language": "en-US"
    }
    ```

!!! Warning
    You cannot update any user field. The `phone` and `email` fields can only be set once. You can update these fields: `external_id`, `first_name`, `last_name`, `counter_account`, `language`

## Search

You can query each entity collection with the `query` parameter e.g. `GET /users/?query=…`. Learn more in the [Handling collection responses](/advanced_topics/collections/) section.

=== "Request"

    ```HTTP hl_lines="1"
    GET /users
        ?query=email+eq+'jane*' HTTP/1.1
    Host: api.sandbox.investsuite.com
    Authorization: Bearer {string}
    ```

=== "Response (body)"

    ```JSON 
    {
        "external_id": "unique_external_entity_id",
        "id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "creation_datetime": "2021-02-18T08:21:02+00:00",
        "version": 3,
        "version_datetime": "2021-02-18T08:21:02+00:00",
        "version_authored_by_user_id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "deleted": false,
        "first_name": "Jane",
        "last_name": "Doe",
        "email": "jane.doe@example.com",
        "phone": "+123456789",
        "language": "en-US"
    }
    ```

## Delete a user

Given the right permissions you can delete any object by issuing a `DELETE` request.

=== "Request"

    ```HTTP hl_lines="1"
    DELETE /users/U01F8YW5NJXMF78PMFKXTE2R7Q7 HTTP/1.1
    Host: api.sandbox.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json
    Authorization: Bearer {string}
    ```

=== "Response (body)"

    ```JSON 
    {
        "external_id": "unique_external_entity_id",
        "id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "creation_datetime": "2021-02-18T08:21:02+00:00",
        "version": 3,
        "version_datetime": "2021-02-18T08:21:02+00:00",
        "version_authored_by_user_id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "deleted": true,
        "first_name": "Jane",
        "last_name": "Doe",
        "email": "jane.doe@example.com",
        "phone": "+123456789",
        "language": "en-US"
    }
    ```