# Overview: Authentication Endpoint

The Authentication Endpoint is designed to manage user access and secure interactions with the API. It provides mechanisms for registering, logging in, refreshing access tokens, logging out and validating user credentials to ensure that only authorized users can access the system's resources.

## Authentication with JWT

ProjectPath-Pro uses JSON Web Tokens (JWT) to secure API interactions and manage user sessions. JWT is a compact and self-contained token format that ensures secure authentication while minimizing server-side overhead.

## Base URL

All authentication-related endpoints are available under the following base URL:

```bash
http://localhost:8080/auth
```

## Available Endpoints

Here are the key authentication endpoints and their purposes:

**User Registration** (`POST /register`)

Allows new users to register by providing credentials: `fullName`, `username`, `email`, `dateOfBirth`, and `password`

This endpoint saves the user in the database and generates an access token and a refresh token for that user. It also creates the access token object and saves it to the database. 

Returns `ResponseEntity<TokenResponse>`

---

**User Login** (`POST /login`)

Authenticates users and issues an access token. 

To perform the authentication, the `email` and `password` credentials are needed in the request.

If the user exists, the service generates an access token and a refresh token. Else, an exception is thrown. 

All past tokens are revoked and the new access token is saved in the database as described in `/register` endpoint.

This behavior happens because the system only allows one login per user. Only one client can have access to the system at a time, meaning that if a user logs in from a new device or session, the previous session will be terminated to maintain the single-login policy.

Returns `ResponseEntity<TokenResponse>`

---

**Token Refresh** (`POST /refresh`)

Issues a new access token maintaining user sessions without reauthentication.

Requires an `Authorization` header that must contain the refresh token, prefixed with `"Bearer"`. This token is used to authenticate the refresh request.

The service does the following checks (note that `authHeader` contains the `Authorization` header): 

1. If the `authHeader` is `null` OR the `authHeader` doesn't starts with `"Bearer "` (notice the empty space) throws an `IllegalArgumentException` saying "Invalid Bearer token".
2. After substracting the refresh token from the `authHeader` and extracting the email from the refresh token, the user email value is checked. If it is `null`, then an `IllegalArgumentException` is thrown with the message "Invalid refresh token".
3. Then, the user is searched in the database by his email. If the user is not found, a `UsernameNotFoundException` is thrown containing the user email. 
4. The service checks if the refresh token is valid via the method `isTokenValid` from `JwtService` class. If it isn't valid, then a `IllegalArgumentException` is thrown with the message "Invalid Refresh Token".

After all these checks, the `JwtService` is used with its method `generateToken` to return an access token (in `String` format), the past user tokens are revoked and finally the new access token is saved in the database as described in `/register`. 

Returns `TokenResponse`

---

**Logout** (`POST /logout`)

Invalidate tokens to log out users securely.

To do so, the system requires an `Authorization` header that must contain a valid access token, prefixed with `"Bearer"`.

The system will perform a `logout` method that will do the following checks (note that `token` contains the `Authorization` header. It's the same as the previous `authHeader`): 

1. If the `token` is `null` OR the `token` doesn't starts with `"Bearer "` (notice the empty space) throws an `IllegalArgumentException` saying "Invalid token".
2. The system will extract the access token from the `Authorization` header (`token` variable in this case). It will then search for the token in the database. If the token doesn't exists in the database, an `IllegalArgumentException` is thrown with the message "Invalid token".

The `logout` method will finally invalidate this access token, save the changes in the database to make them effective and the system is going to clean the security context to delete any trace of that user's authentication.