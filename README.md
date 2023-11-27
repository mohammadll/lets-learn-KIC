# kong-ingress-controller (KIC)
![Kong Image](kong.png)

## Description
Kong Ingress Controller allows you to run `Kong Gateway` as a Kubernetes Ingress to handle inbound requests for a Kubernetes cluster.
Kong Ingress Controller takes Kubernetes resources such as `Ingress` and `HTTPRoute` and converts them into a valid Kong Gateway configuration. It enables you to use all the features of Kong Gateway.


## Table of Contents
 - Kong Ingress Setting-up [Link](00-kong-ingress-setting-up)
 - Basic HTTP Route Using Gateway API [Link](01-kong-ingress-basic)
 - host and path rewrites [Link](02-kong-ingress-req-manipulation)
 - HTTPRoute Multiple Backends [Link](03-kong-ingress-multiple-backends)
 - TLS [Link](04-kong-ingress-tls)
 - Expose a service located outside the Kubernetes cluster [Link](05-kong-ingress-external-service)
 - Create TCP routing configuration for Kong Gateway in Kubernetes using TCPRoute [Link](06-kong-ingress-tcp-route)
 - Authentication & Rate Limiting & ACL kong plugins [Link](07-kong-ingress-plugins)
