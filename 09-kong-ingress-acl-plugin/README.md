# Follow the below steps to do this scenario
To proxy requests, you need an upstream application to send a request to. Deploying `echo` service:

    kubectl apply -f echo-app.yml

## Create Gateway-Class and Gateway Resources

    kubectl apply -f kong-gateway.yml

## Create two different HTTPRoute resources

    kubectl apply -f http-route-path-1.yml 
    kubectl apply -f http-route-path-2.yml
The first HTTPRoute is responsible to route `/lemon` requests to `echo` service and the second one is responsible to route `/lime` requests to `echo` service. so if you type `curl -i $PROXY_IP/lemon` or `curl -i $PROXY_IP/lime`, You'll receive `200` response code.
