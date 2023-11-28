# Multiple Backends
HTTPRoute supports adding multiple Services under its `BackendRefs` field. When you add `multiple Services`, requests through the `HTTPRoute` are `distributed` across the `Services`. This guide walks through creating an HTTPRoute with multiple backend Services.

## Deploy multiple Services with HTTPRoute
In this scenario, We're going to deploy two different services to show how multiple backends work. There are two `HTML` files in directory `kong-ingress-multi-backends/templates`, each representing a simple web page. We're going to create a `deployment` and a `service` for each of them. But before that, we need to build them using `Dockerfile` located inside directory `kong-ingress-multi-backends` and use the resulting `image` in the deployment. In the end, we'll have two `deployments` and two `services`. We're going to place these two services in the `backendRefs` of `HTTPRoute`.

# Follow the below steps to do this scenario

## Create Docker Image for our simple apps

    cd kong-ingress-multi-backends
    docker build -t kong-ingress-multi-backends .

## Create Deployment and Service resources for our simple apps

    kubectl apply -f app-v1-deployment.yml
    kubectl apply -f app-v1-service.yml
    kubectl apply -f app-v2-deployment.yml
    kubectl apply -f app-v2-service.yml

## Create Kong Gateway-Class And Gateway resources

    kubectl apply -f kong-gateway.yml

## Create HTTPRoute Resources to route the requests to those two services

    kubectl apply -f simple-apps-http-route.yml

Make sure `simples-apps.isc` is available in your `/etc/hosts` and points to `external-ip` of `kong-gateway-proxy` service. Then open your browser and type:

    http://simple-apps.isc
If you refresh your web page, The traffic is distributed equally between two services

## Change the weight of traffic
If you want to change the weight of traffic for our services, Uncomment `weight` in `backendRefs` field of `simple-apps-http-route.yml` and set the weight based on your needs and then:

    kubectl apply -f simple-apps-http-route.yml
If you refresh your web page, You'll see that traffic is distributing based on `weight` . for example if the weight of app-v1 is `300` and the weight of app-v2 is `100`, it means that if you send four requests, three of them will be routed to app-v1 and one of them will be routed to app-v2
