
# Authentication Flows

## Standard User Flows

### Registration

The user makes a registration request, providing and email address. A new record is stored in a table that tracks ongoing registration requests. These registration records only live for a short time (30 minutes?) before being deleted, so a user must complete registration once they start, or they have to start over.

```http
HTTP/1.1 POST /auth/registration
Content-Type: application/json

{
	"email": "someone@example.com"
}
```

A client token specifically for the purpose of this request is sent back to the requester.

```http
HTTP/1.1 201 Created
Content-Type: application/json

{
	"requestId": 123,
	"clientToken": "client token goes here"
}
```

A second token is sent to the provided email address to validate the email address. The user must then provide both tokens, as well as any additional required information to complete the registration.

```http
HTTP/1.1 PUT /auth/registration/123
Content-Type: application/json

{
	"clientToken": "client token goes here",
	"emailToken": "email token goes here",
	"securityQuestions": [
		{
			"questionId": 1,
			"answer": "answer goes here"
		}
	]
}
```

If the tokens check out, and the account looks good, we complete the process by creating the new user record. The UI would then prompt the user to login to their new account.

```http
HTTP/1.1 200 OK
```



### Login

The user provides their email address and password in a request to login

```http
HTTP/1.1 POST /auth/user-token
Content-Type: application/json

{
	"email": "someone@example.com",
	"password": "password goes here"
}
```

If the credentials check out, we prompt the user to provide MFA credentials (if needed).

```http
HTTP/1.1 202 Accepted
Content-Type: application/json

{
	"requestId": 123,
	"clientToken": "client token goes here",
	"challenge": {
		"challengeId": 987,
		"type": "securityQuestion",
		"question": "some security question text here"
	}
}
```

The user must then answer the challenge with the appropriate response to complete loging in.

```http
HTTP/1.1 PUT /auth/user-token/123
Content-Type: application/json

{
	"clientToken": "client token goes here",
	"challenge": {
		"challengeId": 987,
		"response": "security question answer here"
	}
}
```

If the answer checks out, we provide the user with their new tken

```http
HTTP/1.1 201 Created
X-Auth-Token: new-user-token-here
```



## App Authentication Flows

Application makes request to create a new token on the user's behalf, providing their own API key (so we can validate that they're allowed to do that) and the user access key that was provided to that app to use.

```http
HTTP/1.1 POST /auth/access-token
X-App-Api-Key: app-api-key-goes-here
X-User-Access-Key: user-specific-key-goes-here
```

We send back a response with the newly created token and a TTL (so the app knows when the token expires).

```http
HTTP/1.1 201 Created
X-Auth-Token: new-user-token-here
X-Token-TTL: 1000000
```

All later requests pass the generated user token for as long as it is valid

```http
HTTP/1.1 GET /some/data
X-Auth-Token: new-user-token-here
```

The application can repeat the first request at any time to get a new token with refreshed TTL
