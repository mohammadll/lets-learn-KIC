# Gateway API
Gateway API is a set of resources for configuring networking in Kubernetes. It expands on Ingress to configure additional types of routes such as TCP, UDP, and TLS in addition to HTTP/HTTPS, and to support backends other than Service, and manage the proxies that implement routes.

A Gateway resource describes an application or cluster feature that can handle Gateway API routing rules, directing inbound traffic to Services

Kong Ingress Controller Document: `The Ingress resource will continue to be supported in Kong Ingress Controller, but we highly recommend that new users adopt Gateway API resources such as HTTPRoute.`

I highly recommend that you read the following links to become more familiar with the gateway API
  - https://gateway-api.sigs.k8s.io/
  - https://docs.konghq.com/kubernetes-ingress-controller/latest/gateway-api/
  - https://docs.konghq.com/kubernetes-ingress-controller/latest/concepts/gateway-api/


# Follow the below steps to do this scenario
To create a GatewayClass and Gateway with Kong Ingress Controller run the following:

    kubectl apply -f kong-gateway.yml

To proxy requests, you need an upstream application to send a request to. Deploying hashicorp/vault service:

    kubectl apply -f vault-deployment.yml
    kubectl apply -f vault-service.yml
Make sure that the `hostname` in `HTTPRoute` is available in your /etc/hosts file . it points to external-ip of kong-gateway-proxy service

Create routing configuration to proxy `/` requests to the hashicorp/vault service:

    kubectl apply -f vault-http-route.yml

Test the routing rule:

    curl -i http://vault.isc
