# Kong authentication plugin
Authentication is the process of verifying that a requester has permissions to access a resource. API gateway authentication authenticates the flow of data to and from your upstream services. Kong Gateway has a library of plugins that support the most widely used methods of API gateway authentication.

## Common authentication methods include:
  - Key Authentication
  - Basic Authentication
  - OAuth 2.0 Authentication
  - LDAP Authentication Advanced
  - OpenID Connect

## Authentication benefits:
With Kong Gateway controlling authentication, requests won’t reach upstream services unless the client has successfully authenticated. This means upstream services process pre-authorized requests, freeing them from the cost of authentication, which is a savings in compute time and development effort.

In this scenario, We'll provide thress different examples:
  - Key Authentication
  - Basic Authentication
  - Using multiple authentication methods simultaneously (Key and Basic authentication methods)

We've created a seperate directory for each of them, inside which there is also a `README`
