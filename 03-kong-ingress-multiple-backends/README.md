# Multiple Backends
HTTPRoute supports adding multiple Services under its `BackendRefs` field. When you add `multiple Services`, requests through the `HTTPRoute` are `distributed` across the `Services`. This guide walks through creating an HTTPRoute with multiple backend Services.

## Deploy multiple Services with HTTPRoute
In this scenario, We're going to deploy two different services to show how multiple backends work. There are two `HTML` files in directory `kong-ingress-multi-backends/templates`, each representing a simple web page. We're going to create a `deployment` and a `service` for each of them. But before that, we need to build them using `Dockerfile` located inside directory `kong-ingress-multi-backends` and use the resulting `image` in the deployment. In the end, we'll have two `deployments` and two `services`. We're going to place these two services in the `backendRefs` of `HTTPRoute`.
