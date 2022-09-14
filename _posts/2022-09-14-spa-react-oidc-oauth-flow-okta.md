---
toc: true
layout: post
description: The flow between Client, Resource Server and Auth server
categories: ["Authentication", "Single Sign On", "SSO"]
title: An ASCII Art Guide to OAuth and OpenID Connect (with added SPA)
image: http://howapped.com/assets/files/contacts_oidc_flow.pdf
hide: false
---

The following shows the OAuth2.0 flow for granting access to your API, with the OpenID connect layer for communicating identity information about the user.



### Simple OIDC Flow between Resource Server and Auth Server

```
1. Resource server (our api) >->->->->->->->->-> Auth server (Okta)
  - API route: /authorization-code/login
  - Description: redirect to auth server login.

    The resource service and therefore client, expects to receive an authorisation code

  - Python function: authorize()
  - Example login url
    - https://newscorp.oktapreview.com/oauth2/default/v1/authorize?client_id=0oa10kbogxjjvDCUW0h8&response_type=code&scope=openid profile email&state=cd0348b84d8f45a8808a39d5763b49a3&nonce=c96bdb88ef184a8caaedeefd366e8881&redirect_uri=http://127.0.0.1:3001/authorization-code/callback

2. Resource server (our api) <-<-<-<-<-<-<-<-<-< Auth server (Okta)
  - API route: /authorization-code/callback
  - Description: Get tokens

    Swap the auth_code and client_secret for an access token.
    This is the key that the client will use from that point
    forward to communicate with the resource server.

  - Python function: callback()
  - Example to get tokens by providing client_secret:
    - https://newscorp.oktapreview.com/oauth2/default/v1/token?client_id=0oa10kbogxjjvDCUW0h8&client_secret=5ketFKpwhMGGSnpJyMNLiYlQfrLxhksJDTerqwof&grant_type=authorization_code&redirect_uri=http://127.0.0.1:3001/authorization-code/callback&code=DpgBENTLyEV0ZlG9MTv9kGWaTMH2G8nnoh0PejbUvAE

```

### Advanced OIDC Flow between SPA, Resource Server and Auth Server

Front Channel means non-trusted communication from the browser

```
⎼⎼⎼⎼>
```

Back Channel means 'trust in the connection'

```
⎼ ⎼ ⎼>
```

```
||     🧔       ||      ||        💻         ||       ||         💻        ||
=============================================================================
1. SPA (F/E)      ⎼⎼⎼⎼>   Resource Server (API)              Auth Server (Okta)
  - XHR request in JS to /api/login
  - API gets secrets for authorize url (eg client_id)
  - API returns login_url containing authorize url for Okta Response payload


2. SPA (F/E)      <⎼ ⎼ ⎼   Resource Server (API)              Auth Server (Okta)
  - API returns json object with login url
  - {"login_url": https://newscorp.oktapreview.com/oauth2/default/v1/authorize?client_id=0oa10kbogxjjvDCUW0h8&response_type=code&scope=openid profile email&state=cd0348b84d8f45a8808a39d5763b49a3&nonce=c96bdb88ef184a8caaedeefd366e8881&redirect_uri=http://127.0.0.1:3001/authorization-code/callback}


3. SPA (F/E)      ⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼>   Auth Server (Okta)
  - Frontend pushes user to the login_url


4. SPA (F/E)      <⎼ ⎼ ⎼ ⎼ ⎼ ⎼ ⎼ ⎼ ⎼ ⎼ ⎼ ⎼ ⎼ ⎼ ⎼ ⎼ ⎼ ⎼ ⎼ ⎼   Auth Server (Okta)
  - The user will receive Okta challenge login page


5. SPA (F/E)      ⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼>   Auth Server (Okta)
  - User submits credentials and/or 2FA


6. SPA (F/E)      <⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼⎼   Auth Server (Okta)
  - If credentials are good the callback on the F/E can proceed
  - The authorisation code is granted. ("code grant")
  - GET https://{CLIENT_URL}/callback?code={UNIQUE_CODE}


7. SPA (F/E)      ⎼⎼⎼⎼>   Resource Server (API)              Auth Server (Okta)
  - XHR request in JS to /api/callback?code={UNIQUE_CODE}


8. SPA (F/E)              Resource Server (API)     ⎼⎼⎼⎼>    Auth Server (Okta)
  - The code grant is then used to retrieve an access token from Okta
  - The API has the client secret. We have kept it secure and not exposed in in frontend.
  - POST https://{OKTA_DOMAIN}/token?code={UNIQUE_CODE}&{CLIENT_ID}&client_secret={CLIENT_SECRET}

9. SPA (F/E)              Resource Server (API)     <⎼ ⎼ ⎼   Auth Server (Okta)
  - Okta replies to our API with the access token
  - status=200, data={ACCESS_TOKEN}
┌────────────────────────────────────────────────────────────────────────────────────────┐
│ Optional - user info                                                                   │
│                                                                                        |
│  SPA (F/E)               Resource Server (API)  ───────►     Auth Server (Okta)        |
│  - Access /userinfo using access token                                                 |
│  - https://{OKTA_DOMAIN}/userinfo header {Authorization: Bearer {ACCESS_TOKEN}         |
│                                                                                        │
│  SPA (F/E)                Resource Server (API) ◄─ - - - -   Auth Server (Okta)        │
│  - status=200, data={USER_INFO}                                                        │
│                                                                                        │
└────────────────────────────────────────────────────────────────────────────────────────┘

10  SPA (F/E)   <⎼ ⎼ ⎼       Resource Server (API)             Auth Server (Okta)
  - Generate a JWT on our API and send back to frontend to avoid exposing the Okta token
  - status=200, data={JWT_TOKEN}


 ┌──────────────────────────────────────────────────────────────────────────────┐
 │                                                                              │
 │                        🎆 State = User Authenticated 🎆                      │
 │                                                                              │
 └──────────────────────────────────────────────────────────────────────────────┘
