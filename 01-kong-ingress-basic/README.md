# Gateway API
Gateway API is a set of resources for configuring networking in Kubernetes. It expands on Ingress to configure additional types of routes such as TCP, UDP, and TLS in addition to HTTP/HTTPS, and to support backends other than Service, and manage the proxies that implement routes.

A Gateway resource describes an application or cluster feature that can handle Gateway API routing rules, directing inbound traffic to Services

Kong Ingress Controller Document: `The Ingress resource will continue to be supported in Kong Ingress Controller, but we highly recommend that new users adopt Gateway API resources such as HTTPRoute.`

I highly recommend that you read the following links to become more familiar with the gateway API
  - https://docs.konghq.com/kubernetes-ingress-controller/latest/gateway-api/
  - https://docs.konghq.com/kubernetes-ingress-controller/latest/concepts/gateway-api/
