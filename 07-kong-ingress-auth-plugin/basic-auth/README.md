# Basic Authentication method
Add Basic Authentication to a service or a route with username and password protection. The plugin checks for valid credentials in the `Proxy-Authorization` and `Authorization` headers (in that order).

# Follow the below steps to do this scenario

To proxy requests, you need an upstream application to send a request to. Deploying hashicorp/vault service:

    kubectl apply -f vault-deployment.yml
    kubectl apply -f vault-service.yml

## Create Gateway-Class and Gateway Resources

    kubectl apply -f kong-gateway.yml

## Create secret resource to store username and password credentials

    kubectl apply -f user-basic-auth-secret.yml
Username is: `mohammad`, Password is: `lets-learn-kong-ingress`

## Create basic-auth kong plugin

    kubectl apply -f kong-plugin.yml

## Create kong consumer

    kubectl apply -f kong-consumer.yml
There is a field named `credentials` in `kong-consumer.yml` which refer to the secret that we created

## Create HTTPRoute

    kubectl apply -f vault-http-route.yml

## Test the credentials:
  - If you type `curl https://vault.isc -k`, You'll receive `Unauthorized` message
  - If you type `curl https://vault.isc -k -u mohammad:SOMETHINGWRONG`, You'll receive `Invalid authentication credentials` message
  - If you type `curl https://vault.isc -k -u mohammad:lets-learn-kong-ingress`, It shows that you authorized successfully
