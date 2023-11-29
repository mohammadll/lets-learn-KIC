# Rate-Limiting
Kong can rate limit traffic without any external dependency. Kong stores the request counters `in-memory` and each Kong node applies the rate limiting policy `independently` without `synchronization of information`. However, if `Redis` is available in your cluster, Kong can take advantage of it and `synchronize` the rate limit information across `multiple Kong nodes` and enforce a slightly different rate limiting policy. You can use Redis for rate limiting in a multi-node Kong deployment.

So If you're using a multi-node Kong deployment, it's better to use `redis` instead of `local` as the policy of your rate-limiting

In this scenario, we're going to scale `kong-gateway` deployment to `3 replicas` and then use redis to synchronize the rate limit information across multiple Kong nodes.

# Follow the below steps to do this scenario

If you look at the first section of this repo (`00-kong-ingress-setting-up`), we deployed redis to our kubernetes cluster. if redis is not available in your cluster, run the following command:

    kubectl create -n kong secret generic redis-password-secret --from-literal=redis-password=PASSWORD
    helm install -n kong redis oci://registry-1.docker.io/bitnamicharts/redis \
    --set auth.existingSecret=redis-password-secret \
    --set architecture=standalone

  ## Scale `kong-gateway` deployment

    kubectl scale --replicas 3 -n kong deployment kong-gateway

## Create rate-limiting kong plugin

    kubectl apply -f rate-limiting-kong-plugin.yml

Some important  fields are available in `rate-limiting-kong-plugin.yml`:
  - The minute is `5` which means we're allowd to send `5` requests per `minute`. you can change it based on your needs
  - The `policy` is `redis`
  - The `redis host` is the name of redis service
  - The `redis password` that we provided when installing redis using helm

## Create HTTPRoute resource

    kubectl apply -f vault-http-route.yml

## Test rate limiting with a multi-node Kong deployment

    for i in `seq 6`; do curl -ksv https://vault.isc 2>&1 | grep "< HTTP"; done
You'll receive `307` response code for 5 times. but the sixth response is `429` wchich means you sent too many requests 

## after 1 minute, try the following command:
    for i in `seq 10`; do curl -ksv https://vault.isc 2>&1 | grep -i "X-RateLimit-Remaining-Minute"; done
The counters decrement sequentially regardless of the Kong Gateway replica count.

`policy: local` setting in the plugin configuration tracks request counters in each Pod’s local memory separately. Counters are not synchronized across Pods, so clients can send requests past the limit without being throttled if they route through different Pods. because of that, we decided to use `policy: redis` to synchronize the rate limit information across multiple Kong nodes.
