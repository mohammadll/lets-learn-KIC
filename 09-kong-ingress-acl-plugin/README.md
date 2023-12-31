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

Uncomment `annotations` and `konghq.com/plugins: 'app-jwt'` in `echo-app.yml` and then apply `echo-app.yml`. so now if you type `curl -i $PROXY_IP/lemon` or `curl -i $PROXY_IP/lime`, You'll receive `401 Unauthorized` response code

## Provision JWT credentials

Go to https://jwt.io/ and choose `rsa256` as your `Algorithm`. then replace its `payload` with the below content:

    {
      "iss": "admin-issuer"
    }
Save admin token:

    export ADMIN_JWT=ey....
Create a secret fot admin_jwt:

    kubectl create secret \
    generic admin-jwt  \
    --from-literal=kongCredType=jwt  \
    --from-literal=key="admin-issuer" \
    --from-literal=secret="admin-issuer" \
    --from-literal=algorithm=RS256 \
    --from-literal=rsa_public_key="-----BEGIN PUBLIC KEY-----
    REPLACE YOUR OWN RSA PUBLIC KEY HERE
     -----END PUBLIC KEY-----"
You can find public-key when you choose `rsa256` Algorithm .

do the same for normal user but replace `admin-issuer` with `user-issuer` for the payload:

    {
      "iss": "user-issuer"
    }
Save normal user token:

    export USER_JWT=ey....
Create its secret and replace `admin-issuer` with `user-issuer`

Go to `admin-kong-consumer.yml` and uncomment `credentials` and `admin-jwt`, then Go to `user-kong-consumer.yml` and uncomment `credentials` and `user-jwt`, then:

    kubectl apply -f admin-kong-consumer.yml
    kubectl apply -f user-kong-consumer.yml

## Create an ACL kong plugin that allows only the admin group

    kubectl apply -f admin-acl-kong-plugin.yml
## Create an ACL kong plugin that allows both the admin and user group

    kubectl apply -f all-acl-kong-plugin.yml

Go to `http-route-path-1.yml` and `http-route-path-2.yml`, then Uncomment `konghq.com/plugins` in these two files and then:

    kubectl apply -f http-route-path-1.yml
    kubectl apply -f http-route-path-2.yml

## Add consumers to groups through credentials.

    kubectl apply -f admin-acl-secret.yml
    kubectl apply -f user-acl-secret.yml

Go to `admin-kong-consumer.yml` and Uncomment item `admin-acl`, then Go to `user-kong-consumer.yml` and Uncomment item `user-acl`. then:

    kubectl apply -f admin-kong-consumer.yml
    kubectl apply -f user-kong-consumer.yml

## Test the ACL Kong plugin
- If you type `curl -sI $PROXY_IP/lemon -H "Authorization: Bearer ${ADMIN_JWT}"`, You'll receive `200` response code
- If you type `curl -sI $PROXY_IP/lime -H "Authorization: Bearer ${ADMIN_JWT}"`, You'll receive `200` response code
- If you type `curl -sI $PROXY_IP/lime -H "Authorization: Bearer ${USER_JWT}"`, You'll receive `200` response code
- If you type `curl -sI $PROXY_IP/lemon -H "Authorization: Bearer ${USER_JWT}"`, You'll receive `403 Forbidden` response code
