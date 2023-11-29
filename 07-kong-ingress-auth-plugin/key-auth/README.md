# Key Authentication
This plugin lets you add API key authentication to a service or a route. Consumers then add their API key either in a query string parameter, a header, or a request body to authenticate their requests.

# Follow the below steps to do this scenario

To proxy requests, you need an upstream application to send a request to. Deploying hashicorp/vault service:

    kubectl apply -f vault-deployment.yml
    kubectl apply -f vault-service.yml

## Create Gateway-Class and Gateway Resources

    kubectl apply -f kong-gateway.yml

## Create secret resource to store `API key` credential

    kubectl apply -f user-key-secret.yml

## Create key-auth kong plugin

    kubectl apply -f kong-plugin.yml
The key is: `lets_learn_kong_ingress`

## Create kong consumer

    kubectl apply -f kong-consumer.yml
There is a field named `credentials` in `kong-consumer.yml` which refer to the secret that we created

## Create HTTPRoute

    kubectl apply -f vault-http-route.yml

## Test the credentials:
  - If you type `curl https://vault.isc -k`, You'll receive `No API key found in request` message
  - If you type `curl https://vault.isc -k -H 'apikey: SOMETHINGWRONG'`, You'll receive `Invalid authentication credentials` message
  - If you type `curl https://vault.isc -k -H 'apikey: lets_learn_kong_ingress'`, It shows that you authorized successfully
