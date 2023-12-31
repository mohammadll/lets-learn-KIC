# kong-ingress-controller (KIC)
![Kong Image](kong.png)

## Description
Kong Ingress Controller allows you to run `Kong Gateway` as a Kubernetes Ingress to handle inbound requests for a Kubernetes cluster.
Kong Ingress Controller takes Kubernetes resources such as `Ingress` and `HTTPRoute` and converts them into a valid Kong Gateway configuration. It enables you to use all the features of Kong Gateway.


## Table of Contents
 - Kong Ingress Setting-up [README](00-kong-ingress-setting-up)
 - Basic HTTP Route Using Gateway API [README](01-kong-ingress-basic)
 - host and path rewrites [README](02-kong-ingress-req-manipulation)
 - HTTPRoute Multiple Backends [README](03-kong-ingress-multiple-backends)
 - TLS [README](04-kong-ingress-tls)
 - Expose a service located outside the Kubernetes cluster [README](05-kong-ingress-external-service)
 - Create TCP routing configuration for Kong Gateway in Kubernetes using TCPRoute [README](06-kong-ingress-tcp-route)
 - Authentication kong plugin [README](07-kong-ingress-auth-plugin)
     - basic-auth authentication method [README](07-kong-ingress-auth-plugin/basic-auth)
     - key-auth authentication method [README](07-kong-ingress-auth-plugin/key-auth)
     - using multiple authentication methods simultaneously [README](07-kong-ingress-auth-plugin/multiple-auth-options)
 - Rate Limiting kong plugin [README](08-kong-ingress-rate-limiting-plugin)
 - ACL kong plugin [README](09-kong-ingress-acl-plugin)
