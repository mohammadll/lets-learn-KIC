# Proxy TCP requests
Create TCP routing configuration for Kong Gateway in Kubernetes using either the TCPIngress custom resource or TCPRoute and TLSRoute Gateway APIs resource. TCP-based Ingress means that Kong Gateway forwards the TCP stream to a Pod of a Service that’s running inside Kubernetes.

## Expose additional ports
Kong Gateway does not include any TCP listen configuration by default. To expose TCP listens, we need to update the `kong-gateway` Deployment’s environment variables and port configuration . BUT if you look at the first section of this repository(`00-kong-ingress-setting-up`), we made all these changes by modifying some `values` of `kong/ingress` helm chart.

These are the values that we modified in `00-kong-ingress-setting-up/values.yml`

    gateway:
      proxy:
        stream:
          - containerPort: 9000
            servicePort: 9000
            protocol: TCP
            nodePort: 32080
    controller:
      ingressController:
        env:
          # Required for all Gateway API stuff that still belongs to the experimental channel, e.g. TCPRoute.
          feature_gates: GatewayAlpha=true
Change port-numbers if you want or use these port-numbers

In this scenario, We're going to deploy mongo database with 1 replica to show you how TCPRoute works. we want to route TCP requests of port `9000` to mongo service

I'm going to connect to mongo shell when i type `mongosh $PROXY_IP:9000`

# Follow the below steps to do this scenario

## Connectivity to Kong
Kubernetes exposes the proxy through a Kubernetes service. Run the following commands to store the load balancer IP address in a variable named `PROXY_IP`:

    export PROXY_IP=$(kubectl get svc --namespace kong kong-gateway-proxy -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
    echo $PROXY_IP

## Deploy mongo DB by applying its statefulset and service resources

    kubectl apply -f mongo-sts.yml
    kubectl apply -f mongo-svc.yml

## Create Gateway-Class and Gateway resources
A new listener named `stream-9000` has beed added to the gateway to allow tcp routing

    kubectl apply -f kong-gateway.yml

## Create TCPRoute resource
This TCPRoute is responsible to route TCP requests of port `9000` to mongo service, if you see the `parentRefs` field of `mongo-tcp-route.yml`, its `sectionName` is the name of listener that we added in the gateway

    kubectl apply -f mongo-tcp-route.yml

## Test The routing rule:

    mongosh $PROXY_IP:9000

You connected to mongo shell successfully
