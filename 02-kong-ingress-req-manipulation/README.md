# Rewriting Hosts and Paths

# Follow the below steps to do this scenario

    kubectl apply -f kong-gateway.yml
    kubectl apply -f echo-app.yml
    kubectl apply -f http-route.yml
    
## Preserve the Host header
Kong Ingress Controller preserves the hostname in the request by default.

    curl -H 'Host:kong.example' "$PROXY_IP/echo?details=true"
You'll see that Host header in the response matches the Host header in the request.

# Use the upstream Host name
You can disable `preserve-host` if you want the Host header to contain the upstream hostname of your service.

Uncomment `annotations` and `konghq.com/preserve-host: 'false'` in `http-route.yml` and then use the below commands:

    kubectl apply -f http-route.yml
    curl -H 'Host:kong.example' "$PROXY_IP/echo?details=true"

## Strip the path
`konghq.com/strip-path` annotation, which strips the path component of the route/Ingress

Uncomment `konghq.com/strip-path: 'true'` in `http-route.yml` and then:

    kubectl apply -f http-route.yml

If you use the below commad, The request upstream now only contains the path components not in the Ingress rule: (`/echo` path will be removed and the url will be `/something`)

    curl -H 'Host:kong.example' "$PROXY_IP/echo/something?details=true"

## Prepend a path
If you want to prepend a value to the upstream path, Add the `konghq.com/path` annotation to your Service, which prepends that value to the upstream path:

Uncomment `annotations` and `konghq.com/path: '/api'` in `echo-app.yml` and the use the below command:

    kubectl apply -f echo-app.yml
    curl -H 'Host:kong.example' "$PROXY_IP/echo/something?details=true"
You'll see that the `URL` in response will be `/api/something` (`/echo` will be removed if `konghq.com/strip-path` is `true`)
