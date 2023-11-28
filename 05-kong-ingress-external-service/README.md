# External Service
Expose a service located outside the Kubernetes cluster using an Ingress.

Read this link https://kubernetes.io/docs/concepts/services-networking/service/#externalname to become more familiar with `ExternalName` Service type

In this scenario, We're going to proxy from `k8s` to `ubuntu.com`

# Follow the below steps to do this scenario

## Create Gateway-Class and Gateway resources

    kubectl apply -f kong-gateway.yml

## Create a Service with type `ExternalName` to proxy to ubuntu

    kubectl apply -f external-service.yml

## Create HTTPRoute resource

    kubectl apply -f http-route.yml

## Test the routing rule
Make sure hostname `ubuntu-from-k8s.kong` is available in your `/etc/hosts` and points to `external-ip` of `kong-gateway-proxy` service and then open your browser and type `http://ubuntu-from-k8s.kong/`. You'll see that your request is routed to `ubuntu.com`.
