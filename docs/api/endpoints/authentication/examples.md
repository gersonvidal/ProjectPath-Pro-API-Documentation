# API Authentication Examples

This page provides examples of requests and responses for the Authentication endpoints: `/register`, `/login`, `/refresh`, and `/logout`.

## Register

### Example Request

```json
{
"fullName": "Pepe Le Pu",
"username": "pepepu19",
"email": "pepepu@example.com",
"dateOfBirth": "1996-04-19", // note the yyyy-MM-dd format
"password": "securepassword123"
}
```

> ⚠️ IMPORTANT: Note that the `dateOfBirth` is in the format of `yyyy-MM-dd`.

### Example Response 

This gives two new tokens: One for access and one for refresh.

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c",
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IlJlZnJlc2ggVG9rZW4iLCJpYXQiOjE2OTcxMDYwMDAsImV4cCI6MTY5NzE5MjQwMH0.KP8QQ5Wfx5sFkfi2RHOfZgNy5JGcULXE6B0ybdplJaM"
}
```

## Login 

### Example Request

```json
{
"email": "pepepu@example.com",
"password": "securepassword123"
}
```

### Example Response 

This gives two new tokens: One for access and one for refresh.

```json
{
  "access_token": "eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwicm9sZXMiOlsiVVNFUiIsIkFETUlOIl0sImlhdCI6MTY5NzExMDAwMCwiZXhwIjoxNjk3MTEzNjAwfQ.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ",
  "refresh_token": "eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI0NTY3ODkwMTIzIiwibmFtZSI6IlJlZnJlc2ggVG9rZW4iLCJpYXQiOjE2OTcxMTAwMDAsImV4cCI6MTY5NzE5NjAwMH0.FJRXKp0qq7J9Md2OeFJzhdCT7qN4P_vNyoZRrfYkAkE"
}
```
## Refresh 

### Example Request

In this case, no body is needed to send. 

Just the `Authorization` header with the value `Bearer refresh_token`

> 📌 NOTE: Replace `refresh_token` with the actual refresh token of the user. 

### Example Response 

This gives one new access token.

The refresh token remains the same as the one of the header.

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI0NTY3ODkwMTIzIiwibmFtZSI6IkphbmUgRG9lIiwicm9sZXMiOlsiVVNFUiJdLCJpYXQiOjE2OTcxMTAwMDAsImV4cCI6MTY5NzExMzYwMH0.sFbJqcjNVb9YJ8Rf0pUbV6XIo3PaAwFZ2s5slOqgZPY",
  "refresh_token": "eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiI0NTY3ODkwMTIzIiwibmFtZSI6IlJlZnJlc2ggVG9rZW4iLCJpYXQiOjE2OTcxMTAwMDAsImV4cCI6MTY5NzE5NjAwMH0.FJRXKp0qq7J9Md2OeFJzhdCT7qN4P_vNyoZRrfYkAkE"
}
```

## Logout

### Example Request

In this case, no body is needed to send. 

Just the `Authorization` header with the value `Bearer access_token`.

> 📌 NOTE: Replace `access_token` with the actual access token of the user. 

### Example Response

The response will return no body and just a `200 OK` when the logout has procceded succesfully.
