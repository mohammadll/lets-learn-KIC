# Using multiple authentication methods at the same time
Learn to configure multiple authentication options for consumers using the Kong Ingress Controller. The default behavior for Kong authentication plugins is to require credentials for all requests even if a request has been authenticated through another plugin. Configure an anonymous consumer on your authentication plugins to set clients authentication options.

We are going to use basic-auth and key-auth authentication methods at the same time

# Follow the below steps to do this scenario

To proxy requests, you need an upstream application to send a request to. Deploying hashicorp/vault service:

    kubectl apply -f vault-deployment.yml
    kubectl apply -f vault-service.yml

## Create Gateway-Class and Gateway Resources

    kubectl apply -f kong-gateway.yml

## Create secret resources for basic-auth and key-auth methods to store their credentials

    kubectl apply -f user-basic-auth-secret.yml
    kubectl apply -f user-key-auth-secret.yml
Basic-auth username: `consumer-1` and password: `consumer-1-password`

Key-auth api key: `consumer-2-password`

## Create basic-auth and key-auth kong plugins

    kubectl apply -f basic-auth-kong-plugin.yml
    kubectl apply -f key-auth-kong-plugin.yml

## Create kong consumers for basic-auth and key-auth secrets

    kubectl apply -f basic-auth-kong-consumer.yml
    kubecrl apply -f key-auth-kong-consumer.yml

## Create HTTPRoute resource

    kubectl apply -f vault-http-route.yml

- If you type `curl https://vault.isc -k`, You'll receive `500` error code
- If you type `curl https://vault.isc -k -u consumer-1:consumer-1-password`, You're still receiving an error even though you have entered usrname and password `correctly`
- If you type `curl https://vault.isc -k -H 'apikey: consumer-2-password'`, It shows that you authorized successfully
  
BUT THERE IS A PROBLEM, Your endpoints are now secure, but neither consumer can access the endpoint when providing valid credentials. This is because each plugin will verify the consumer using it’s own authentication method.

To allow multiple authentication methods, create an anonymous consumer which is the default user if no valid credentials are provided:

## Create request termination kong plugin

    kubectl apply -f anonymous-kong-plugin.yml
    
## Create a consumer named `anonymous`

    kubectl apply -f anonymous-kong-consumer.yml
This consumer has an `annotation` named `konghq.com/plugins` which refer to the name of the anonymous plugin that we created

All requests to the API will now succeed as the anonymous consumer is being used as a default. we added a request termination plugin to the anonymous consumer that returns HTTP 401

## Test the credentials

  - If you type `curl https://vault.isc -k`, You'll receive `401` error code
  - If you type `curl https://vault.isc -k -u consumer-1:SOMETHINGWRONG`, You'll receive `401` error code
  - If you type `curl https://vault.isc -k -u consumer-1:consumer-1-password`, It shows that you authorized successfully
  - If you type `curl https://vault.isc -k -H 'apikey: consumer-2-password'`, It shows that you authorized successfully
