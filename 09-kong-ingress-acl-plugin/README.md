# Access Control List plugin
Configure the Kong ACL Plugin. To use the ACL Plugin you need at least one Authentication plugin. In this example, we're going to use the `JWT Auth Plugin`.

In this scenario, we're going to use access control list. we have a simple app named `echo` and it has two different paths: `/lemon` and `/lime`. `ADMIN` user must be able to access to both of those paths using its `token (jwt)`. `NORMAL` user must be able to access to path `/lime` only.

# Follow the below steps to do this scenario
To proxy requests, you need an upstream application to send a request to. Deploying `echo` service:

    kubectl apply -f echo-app.yml

## Create Gateway-Class and Gateway Resources

    kubectl apply -f kong-gateway.yml

## Create two different HTTPRoute resources

    kubectl apply -f http-route-path-1.yml 
    kubectl apply -f http-route-path-2.yml
The first HTTPRoute is responsible to route `/lemon` requests to `echo` service and the second one is responsible to route `/lime` requests to `echo` service. so if you type `curl -i $PROXY_IP/lemon` or `curl -i $PROXY_IP/lime`, You'll receive `200` response code.

## Create jwt kong plugin

    kubectl apply -f jwt-kong-plugin.yml

## Create two different kong consumers (admin, normal-user)
    kubectl apply -f admin-kong-consumer.yml
    kubectl apply -f user-kong-consumer.yml
