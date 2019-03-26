
# Authentication Flows

## Standard User Flows

### Registration

The user makes a registration request, providing and email address. A new record is stored in a table that tracks ongoing registration requests.

```http
HTTP/1.1 POST /auth/registration
Content-Type: application/json

{"email": "someone@example.com"}
```

A client token specifically for the purpose of this request is sent back to the requester.

```http
HTTP/1.1 201 Created
Content-Type: application/json

{"clientToken": "clienttokengoeshere"}
```

A second token is sent to the provided email address to validate the email address. The user must then provide both tokens to move to the next step.

```http
HTTP/1.1 PUT /auth/registration
Content-Type: application/json

{"clientToken": "clienttokengoeshere", "emailToken": "emailtokengoeshere"}
```

If the tokens check out, we mark the registration record as "email verifies" and send a response to inform the user that they can move on with the next step of registration.

```http
HTTP/1.1 200 OK
```








## App Authentication Flows

Application makes request to create a new token on the user's behalf, providing their own API key (so we can validate that they're allowed to do that) and the user access key that was provided to that app to use.

```http
HTTP/1.1 POST /auth/access-token
X-App-Api-Key: somekeygoeshere
X-User-Access-Key: userspecifickeygoeshere
```

We send back a response with the newly created token and a TTL (so the app knows when the token expires).

```http
HTTP/1.1 201 Created
X-Auth-Token: newusertokenhere
X-Token-TTL: 1000000
```

All later requests pass the generated user token for as long as it is valid

```http
HTTP/1.1 GET /some/data
X-Auth-Token: newusertokenhere
```

The application can repeat the first request at any time to get a new token with refreshed TTL
