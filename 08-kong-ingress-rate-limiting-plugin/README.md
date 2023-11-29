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
