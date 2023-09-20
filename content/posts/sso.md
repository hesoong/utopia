---
title: SSO 笔记
date: 2023-09-20T15:58:07+08:00
draft: false
toc: true
images: 
tags:
  - 笔记
---

## What is SSO?

Single sign-on (SSO) is an authentication method that enables users to securely authenticate with multiple applications and websites by using just one set of credentials.

---

## How Does Single Sign-On (SSO) protocol Work?

There are two commonly used SSO protocols namely SAML and OAuth2.

### OAuth2

#### Role

OAuth defines four roles:

1. resource owner

  >An entity capable of granting access to a protected resource.
  When the resource owner is a person, it is referred to as an
  end-user.

2. resource server

 > The server hosting the protected resources, capable of accepting
  and responding to protected resource requests using access tokens.

3. client

> An application making protected resource requests on behalf of the
  resource owner and with its authorization.  The term "client" does
  not imply any particular implementation characteristics (e.g.,
  whether the application executes on a server, a desktop, or other
  devices).

4. authorization server

  >The server issuing access tokens to the client after successfully
  authenticating the resource owner and obtaining authorization.

#### Protocol Flow

Please refer to this link for the OAuth 2.0 authorization framework details: [RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749#section-4.1)

The main process as below:

![Oauth2 Flow](oauth2flow.png)
The abstract OAuth 2.0 flow illustrated in above figure describes the interaction between the four roles and includes the following steps:

**(A)** - The client requests authorization from the resource owner.  The authorization request can be made directly to the resource owner(as shown), or preferably indirectly via the authorization
server as an intermediary.

**(B)** - The client receives an authorization grant, which is a credential representing the resource owner's authorization, expressed using one of four grant types defined in this specification or using an extension grant type.  The authorization grant type depends on the method used by the client to request authorization and the types supported by the authorization server.

**(C)** - The client requests an access token by authenticating with the authorization server and presenting the authorization grant.

**(D)** - The authorization server authenticates the client and validates the authorization grant, and if valid, issues an access token.

**(E)** - The client requests the protected resource from the resource server and authenticates by presenting the access token.

**(F)** - The resource server validates the access token, and if valid, serves the request.

#### Obtaining Authorization

To request an access token, the client obtains authorization from the resource owner.  The authorization is expressed in the form of an authorization grant, which the client uses to request the access token.  OAuth defines four grant types:

1. ***authorization code***
2. ***implicit***
3. ***resource owner password credentials***
4. ***client credentials***.
It also provides an extension mechanism for defining additional grant types.

#### Authorization Code Grant Flow
>
>The authorization code grant type is used to obtain both access
tokens and refresh tokens and is optimized for confidential clients.
Since this is a redirection-based flow, the client must be capable of
interacting with the resource owner's user-agent (typically a web
browser) and capable of receiving incoming requests (via redirection)
from the authorization server.

![Authorization Code Grant Flow](oauth2authcode.png)
The flow illustrated in Figure 3 includes the following steps:

**(A)** - The client initiates the flow by directing the resource owner's user-agent to the authorization endpoint.  The client includes its client identifier, requested scope, local state, and a redirection URI to which the authorization server will send the user-agent back once access is granted (or denied).

**(B)** - The authorization server authenticates the resource owner (via the user-agent) and establishes whether the resource owner grants or denies the client's access request.

**(C)** - Assuming the resource owner grants access, the authorization server redirects the user-agent back to the client using the redirection URI provided earlier (in the request or during client registration).  The redirection URI includes an authorization code and any local state provided by the client earlier.

**(D)** - The client requests an access token from the authorization server's token endpoint by including the authorization code received in the previous step.  When making the request, the client authenticates with the authorization server.  The client includes the redirection URI used to obtain the authorization code for verification.

**(E)** - The authorization server authenticates the client, validates the authorization code, and ensures that the redirection URI received matches the URI used to redirect the client in step (C).  If valid, the authorization server responds back with an access token and, optionally, a refresh token.

### SAML 2.0

The SAML 2.0(Security Assertion Markup Language) Login feature provides an application with the capability to act as a SAML 2.0 Relying Party, having users log in to the application by using their existing account at a SAML 2.0 Asserting Party (Okta, ADFS, etc).

#### Role

1. Principal
2. Service Provider(SP) - web service(like EDL)
3. Identity Provider(IdP) -  Microsoft, AWS ...

#### Main Process


The main process as below:
![SAML2-flow](samlflow.png)

1. First, a user makes an unauthenticated request to the resource `/private` for which it is not authorized.
2. Spring Security’s [`FilterSecurityInterceptor`](https://docs.spring.io/spring-security/reference/5.6.0-RC1/servlet/authorization/authorize-requests.html#servlet-authorization-filtersecurityinterceptor) indicates that the unauthenticated request is *Denied* by throwing an `AccessDeniedException`.
3. Since the user lacks authorization, the [`ExceptionTranslationFilter`](https://docs.spring.io/spring-security/reference/5.6.0-RC1/servlet/architecture.html#servlet-exceptiontranslationfilter) initiates *Start Authentication*. The configured [`AuthenticationEntryPoint`](https://docs.spring.io/spring-security/reference/5.6.0-RC1/servlet/authentication/architecture.html#servlet-authentication-authenticationentrypoint) is an instance of [`LoginUrlAuthenticationEntryPoint`](https://docs.spring.io/spring-security/site/docs/5.6.0-RC1/api/org/springframework/security/web/authentication/LoginUrlAuthenticationEntryPoint.html) which redirects to [the `<saml2:AuthnRequest>` generating endpoint](https://docs.spring.io/spring-security/reference/5.6.0-RC1/servlet/saml2/index.html#servlet-saml2login-sp-initiated-factory), `Saml2WebSsoAuthenticationRequestFilter`. Or, if you’ve [configured more than one asserting party](https://docs.spring.io/spring-security/reference/5.6.0-RC1/servlet/saml2/index.html#servlet-saml2login-relyingpartyregistrationrepository), it will first redirect to a picker page.
4. Next, the `Saml2WebSsoAuthenticationRequestFilter` creates, signs, serializes, and encodes a `<saml2:AuthnRequest>` using its configured [`Saml2AuthenticationRequestFactory`](https://docs.spring.io/spring-security/reference/5.6.0-RC1/servlet/saml2/index.html#servlet-saml2login-sp-initiated-factory).
5. Then, the browser takes this `<saml2:AuthnRequest>` and presents it to the asserting party. The asserting party attempts to authentication the user. If successful, it will return a `<saml2:Response>` back to the browser.
6. The browser then POSTs the `<saml2:Response>` to the assertion consumer service endpoint.

---

## Usage in Java(Spring Security)

Please refer to this link for details: [Spring Security](https://docs.spring.io/spring-security/reference/servlet/oauth2/login/core.html)

