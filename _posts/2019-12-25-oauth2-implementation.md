---
published: true
layout: post
date: 2019-12-25T00:00:00.000Z
categories: others
---
## Preamble

Internet infrastructure partially depends on standards and conventions given by IETF/IEEE working groups. And it allows web-ecosystem to evolve in a way that every application shouldn't be developed via custom solutions. It's important in b2b area in particular - if we want to give an access to user's data to third-party systems we must really care about simplicity.
Oauth framework is one of those successful projects.

## Oauth 2
Oauth changed a lot since it's first implementation because from some time we can delegate security problems to HTTPS secured transport layer and focus on the resource-sharing problem.

### Concept
Simply put we (resource server) want to provide an option where user (resource owner) can share his data to another application (client).

![oauth flow]({{site.baseurl}}/assets/img/oauth_flow.png)

___

Briefly, this process consists of two main steps:
1. Getting authorization code from authorization server
2. Getting access code by authorization code from resource server

Each of these steps can be divided into deeper levels, but we'll go into details a little later. 
At first let's describe participants:

* Client - mostly is a web application requesting user'data
* Resource owner - target user having permissions to manage the data
* Resource server - application storing data and providing interface to access data
* Authorization server - application providing interface to login user, intenging to share his data

Resource server and authorization server sometimes may be considered as a single server, 
so Oauth separates them just to give us a better understanging.

## Some words on implementing Oauth 2 server (simple)
It doesn't require big effort to implement oauth as a client. Facebook, Google, VK and other companies already provide quite comprehensive documentation. Difficulties encountered when we decide to add oauth-server functionality to concrete web application.

Imagine we have a regular web-application (allows to register via email, login, logout).
Oauth 2 is always about giving an access to an API. So it may be implicitly connected to a role based authorization (role model), where our client may be considered as a special type of user - system user with arbitrary group of permissions. Sounds complex! Detach oauth from any other entity in our application is a good idea, but what happens next in this case? Right, we manage authorization at higher level like this:

```
  if (!findUserByBearerToken(request.token)){
      return 403;//forbidden; oversimplified pseudocode
  }
```

Actually once we get an access token we always act on behalf of a user. Keep in mind:
this approach works only and only if don't use fine-grained role model. That means we separate method access only by: AUTHENTICATED and NOT AUTHENTICATED.

Let's describe our solution in terms of a database tables:
As RFC6749 says, we have a client_id (id of a client application) and client_secret.
```sql
CREATE TABLE IF NOT EXISTS oauth_clients
(
    client_id               TEXT      NOT NULL,
    client_secret           TEXT      NOT NULL,
    name                    TEXT      NOT NULL, -- name of client application
    enabled                 BOOLEAN   NOT NULL  -- in case we block a client
);
```

Then we think about the authorization codes, like:
```sql
CREATE TABLE IF NOT EXISTS oauth_auth_codes
(
	value                   TEXT      NOT NULL, -- a token itself
	expiration_ts           BIGINT    NOT NULL, -- expiration epoch time in milliseconds
	redirect_uri            TEXT      NOT NULL,
	state                   TEXT      NULL,     -- arbitraty information (usually csrf token)
	client_id               TEXT      NOT NULL,
    user_id                 BIGINT    NOT NULL  -- user related to this authorization code
);
```

And the last one: access tokens
```sql
CREATE TABLE IF NOT EXISTS oauth_access_tokens
(
	value                   TEXT      NOT NULL,
	expiration_ts           BIGINT    NOT NULL,
	client_id               TEXT      NOT NULL,
    user_id                 BIGINT    NOT NULL
);
```
The scheme looks quite simple and gives some view. Though we didn't describe the algorithm and how to reissue new access token. 

For more on this topic, please visit [Oauth 2.0 Standard](https://tools.ietf.org/html/rfc6749) (by IETF)

