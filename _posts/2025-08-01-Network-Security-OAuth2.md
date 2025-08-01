---
title: Network Security - OAuth 2.0
date: 2025-08-01
modify_date: 2025-08-01
tags: Network Security
key: Network-Security-OAuth-2025-08-01
---

## OAUTH 2.0 Overview

**OAuth 2.0** is an **authorization framework** that enables third-party applications to obtain limited access to user accounts on an HTTP service, such as GitHub, or Google.

### Key Concepts

1. **Resource Owner**: The user who owns the data and grants access to it.
2. **Client**: The application requesting access to the resource owner's data.
3. **Authorization Server**: The server that authenticates the resource owner and issues access tokens to the client.
4. **Resource Server**: The server hosting the resource owner's data, which accepts access tokens to grant access.
5. **Access Token**: A token issued by the authorization server that the client uses to access the resource server.

<!--more-->

### Sequence of Events

```mermaid
sequenceDiagram
    actor User as User (Resource Owner)
    participant Client as Client (Your App)
    participant AuthServer as Authorization Server
    participant ResourceServer as Resource Server (API)

    Note over User,Client: Initiate Flow
    User->>Client: Requests access to protected resource
    activate Client
    Client->>User: Redirect to Authorization Server
    deactivate Client

    Note over User,AuthServer: Authentication & Consent
    User->>AuthServer: Navigates to auth endpoint
    activate AuthServer
    AuthServer->>User: Presents login prompt
    User->>AuthServer: Enters credentials
    AuthServer->>User: Presents permission request
    User->>AuthServer: Grants consent
    deactivate AuthServer

    Note over AuthServer,Client: Authorization Code
    AuthServer->>Client: Redirects with authorization code
    activate Client

    Note over Client,AuthServer: Token Exchange
    Client->>AuthServer: Sends authorization code + client credentials
    activate AuthServer
    AuthServer->>Client: Returns access token (and optionally refresh token)
    deactivate AuthServer

    Note over Client,ResourceServer: Access Resource
    Client->>ResourceServer: API request with access token
    activate ResourceServer
    ResourceServer->>Client: Returns protected data
    deactivate ResourceServer
    deactivate Client

    Note over User,Client: Display Result
    Client->>User: Shows requested data
```
