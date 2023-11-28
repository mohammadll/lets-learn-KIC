# TLS Termination
Terminate mode listeners decrypt the TLS stream and inspect the request it wraps before passing it upstream. They require `certificate Secret` reference in the `listeners[].tls.[]certificateRefs` field. They accept `HTTPRoutes`, `TCPRoutes`, and `GRPCRoutes`.

To terminate TLS, create a `Gateway` with a listener with `.tls.mode`: `"Terminate"`, create a `TLS Secret` and add it to the listener `.tls.certificateRefs` array, and then create one of the supported route types with matching criteria that will bind it to the `listener`.

# Follow the below steps to do this scenario

To proxy requests, you need an upstream application to send a request to. Deploying hashicorp/vault service:

    kubectl apply -f vault-deployment.yml
    kubectl apply -f vault-service.yml

## Create a TLS secret
If you remember, we created a secret called `"kong-tls"` in the first section of this repository (`00-kong-ingress-setting-up`), if this secret is not available in your cluster, Go to `00-kong-ingress-setting-up` and then:

    cd ../00-kong-ingress-setting-up/
    kubectl apply -f kong-tls-secret.yml

  ## Create Gateway-Class and Gateway Resources
A new listener called `https` has been added to the gateway

    kubectl apply -f kong-gateway.yml

## Create HTTPRoute Resource
There are two different annotations in `vault-http-route.yml` related to `HTTPS`. so `konghq.com/protocols: 'https'` forces you to use `https` instead of `http` in the request, and `konghq.com/https-redirect-status-code: '301'` redirects `http` requests to `https`.

    kubectl apply -f vault-http-route.yml

## Test the routing rule:
  - Open your browser and type `https://vault.isc`, You'll see that your connection is `https` and you'll see our cert info in your browser
  - Open your browser and type `http://vault.isc`, You'll see that your request will be redirected to `https`
