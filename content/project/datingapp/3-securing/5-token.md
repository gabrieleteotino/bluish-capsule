---
title: "Token"
date: 2018-06-12T15:18:19+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "jwt"]
categories: ["dev"]
draft: false
---

<!--more-->

## JWTs

**JSON Web Tokens** is the industry standard RFC 7519 for token based claims and authentication.

Using JWT the server doesn't need to go back to the database (or other authentication services) to verify the authentication of the user.

A JWT is composed of three parts:

- **Header**, in clear text

```
  { "alg": "HS512", "typ": "JWT" }
```

- **Payload**, in clear text (apply care to what is stored here)

```
  {
    "nameid": "8",
    "unique_name": "frank",
    "nbf": 1511110407,
    "exp": 1511196807,
    "iat": 1511110407
  }
```

- **Signature**, an encoded composed of header, payload and secret (known only to the server) used to verify the autenticity of the token.

The header and the payload are converted in a UTF8 array and then encoded with Base64url.
Those two string are then concatenated with the secret and then encoded with HMAC SHA-256.

Pseudocode:

```
signature = HMACSHA256(base64UrlEncode(header) + base64UrlEncode(payload) + secret)
```

The tree parts are then concatenated with a dot separator.

Example JWT (with added CR to make it readable):
```
eyJ0eXAiOiJKV1QiLA0KICJhbGciOiJIUzI1NiJ9.
eyJpc3MiOiJqb2UiLA0KICJleHAiOjEzMDA4MTkzODAsDQogImh0dHA6Ly9leGFtcGxlLmNvbS9pc19yb290Ijp0cnVlfQ.
dBjftJeZ4CVP-mB92K27uhbUJU1p1r_wW1gFWFOEjXk
```

## Login action

To generate a Token we need a secret key, temporary stored directly in the source code, a SecurityTokenDescriptor and a TokenHandler.

Prepare the SecurityTokenDescriptor

```c#
// The key must be stored in a secure configuration
var key = System.Text.Encoding.UTF8.GetBytes("super duper secret");

var tokenDescriptor = new SecurityTokenDescriptor
{
  Subject = new ClaimsIdentity(new Claim[]
  {
    new Claim(ClaimTypes.NameIdentifier, userId),
    new Claim(ClaimTypes.Name, username)
  }),
  Expires = DateTime.Now.AddDays(1),
  SigningCredential = new SigningCredential(
    new SymmetricSecurityKey(key),
    SecurityAlgorithms.HmacSha256Signature
  )
}
```

Create a token using a JwtSecurityTokenHandler

```c#
var tokenHandler = new JwtSecurityTokenHandler();
var token = tokenHandler.CreateToken(tokenDescriptor);
var tokenString = tokenHandler.WriteToken(token);
```

Then return a new object with the tokenString

```c#
return Ok(new {tokenString});
```

To validate the returned token string go to [jwt.io](https://jwt.io)
