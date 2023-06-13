---
sidebar_position: 1
---


Local User Authentication vs Identity Providers
-----------------------------------------------

![Error loading enter.png](./img/enter.png)

Applications often need to identify their users. The simplistic approach is to create a local database for the users' 
accounts and credentials. Given enough technical care this can be made to work well. However, local authentication can
be bad for business:

People find sign up and account creation tedious, and rightly so. Consumer web sites and apps may suffer abandoned
shopping carts because of that, which means loss of business and sales.

For enterprises with many apps, maintenance of separate user databases can easily become an administrative and security 
nightmare. You may want to put your IT resources to better use.

The established solution to these problems is to
**delegate user authentication and provisioning to a dedicated, purpose-built service, called an Identity Provider
(IdP)**.

Google, Facebook and Twitter, where many people on the internet are registered, offer such IdP services for their users.
A consumer website can greatly streamline user on-boarding by integrating login with these IdPs.

In an enterprise, this would ideally be one **internal IdP service**, for employees and contractors to log into the 
internal applications. Centralisation has considerable benefits, such as easier administration and potentially faster 
development cycles for new apps. You may ask: Isn't that going to create a single point of failure? No, not when the IdP 
service is built for redundancy.


OpenID Connect
--------------

![Error loading openid.png](./img/openid.png)

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html), published in 2014, is not the first **standard
for IdP**, but definitely the best in terms of usability and simplicity, having learned the lessons from past efforts such
as [SAML](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language) and OpenID 1.0 and 2.0.

What is the formula for success of OpenID Connect?

- **Easy to consume identity tokens**: Clients receive the user's identity encoded in a secure JSON Web Token (JWT),
  called an ID token. JWTs are appreciated for their elegance and portability, and for their ready support for a wide
  range of signature and encryption algorithms. All that makes JWT outstanding for the ID token job.
- **Based on the OAuth 2.0 protocol**: The ID token is obtained via a standard OAuth 2.0 flow, with support for web 
  applications as well as native / mobile apps. OAuth 2.0 also means having one protocol for authentication and 
  authorisation (obtaining access tokens).
- **Simplicity**: OpenID Connect is simple enough to integrate with basic apps, but it also has the features and security options to match demanding enterprise requirements.


The Identity Token
------------------

![Error loading id-card.png](./img/id-card.png)

The [ID token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) resembles the concept of an identity card,
in a standard [JWT](https://jwt.io/) format, signed by the **OpenID Provider** (**OP**). To obtain one the client needs
to send the user to their OP with an authentication request.

Features of the ID token:

- Asserts the identity of the user, called **subject** in OpenID (sub).
- Specifies the issuing authority (**iss**).
- Is generated for a particular audience, i.e. client (**aud**).
- May contain a nonce (**nonce**).
- May specify when (**auth_time**) and how, in terms of strength (**acr**), the user was authenticated.
- Has an issue (**iat**) and expiration time (**exp**).
- May include additional requested details about the subject, such as name and email address.
- Is **digitally signed**, so it can be verified by the intended recipients.
- May optionally be encrypted for confidentiality. 

- The ID token statements, or claims, are packaged in a simple JSON object:

```json
{
    "sub"       : "alice",
    "iss"       : "https://openid.c2id.com",
    "aud"       : "client-12345",
    "nonce"     : "n-0S6_WzA2Mj",
    "auth_time" : 1311280969,
    "acr"       : "c2id.loa.hisec",
    "iat"       : 1311280970,
    "exp"       : 1311281970
}
```

The ID token header, claims JSON and signature are encoded into a base 64 URL-safe string, for easy passing arround, for 
example as URL parameter.

```
eyJhbGciOiJSUzI1NiIsImtpZCI6IjFlOWdkazcifQ.ewogImlzcyI6ICJodHRw
Oi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ4Mjg5NzYxMDAxIiw
KICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiAibi0wUzZfV3pBMk1qIi
wKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDEzMTEyODA5NzAKfQ.ggW8hZ
1EuVLuxNuuIJKX_V8a_OMXzR0EHR9R6jgdqrOOF4daGU96Sr_P6qJp6IcmD3HP9
9Obi1PRs-cwh3LO-p146waJ8IhehcwL7F09JdijmBqkvPeB2T9CJNqeGpe-gccM
g4vfKjkM8FcGvnzZUN4_KSP0aAp1tOJ1zZwgjxqGByKHiOtX7TpdQyHE5lcMiKP
XfEIQILVq0pc_E2DzL7emopWoaoZTF_m0_N0YzFC6g6EJbOEoRoSK5hoDalrcvR
YLSrQAZZKflyuVCyixEoV9GfNQC3_osjzw2PAithfubEEBLuVVk4XUVrWOLrLl0
nx7RkKU8NXNHq-rvKMzqg
```

We can read more about the JWT data structure and its encoding in [RFC 7519](https://tools.ietf.org/html/rfc7519).


Requesting an ID Token
----------------------

![Error loading walk.png](./img/walk.png)

Now that we know what an [ID token](#the-identity-token) is, how can a client, called **Relying Party** (**RP**) in
OpenID Connect, request one?

The OpenID Connect protocol, in abstract, follows the following steps.

1. The RP (Client) sends a request to the OpenID Provider (OP).
2. The OP authenticates the End-User and obtains authorization.
3. The OP responds with an ID Token and usually an Access Token.
4. The RP can send a request with the Access Token to the UserInfo Endpoint.
5. The UserInfo Endpoint returns Claims about the End-User.

These steps are illustrated in the following diagram:

```
+--------+                                   +--------+
|        |                                   |        |
|        |---------(1) AuthN Request-------->|        |
|        |                                   |        |
|        |  +--------+                       |        |
|        |  |        |                       |        |
|        |  |  End-  |<--(2) AuthN & AuthZ-->|        |
|        |  |  User  |                       |        |
|   RP   |  |        |                       |   OP   |
|        |  +--------+                       |        |
|        |                                   |        |
|        |<--------(3) AuthN Response--------|        |
|        |                                   |        |
|        |---------(4) UserInfo Request----->|        |
|        |                                   |        |
|        |<--------(5) UserInfo Response-----|        |
|        |                                   |        |
+--------+                                   +--------+

```