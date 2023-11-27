# Follow the below steps to configure kong-ingress-controller

## Install the Gateway API CRDs before installing Kong Ingress Controller.
    kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.0.0/standard-install.yaml
    kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.0.0/experimental-install.yaml
## Install the properly configured Kong Kubernetes Ingress Controller
    helm repo update
    helm install kong kong/ingress --values values.yml --namespace kong --create-namespace
## Optional

## Apply kong-tls-secret.yml to use self-signed certificates for the tls examples of this repo
    kubectl apply -f kong-tls-secret.yml

## Deploy Redis to your Kubernetes cluster for the rate-limiting examples of this repo
    kubectl create -n kong secret generic redis-password-secret --from-literal=redis-password=PASSWORD
    helm install -n kong redis oci://registry-1.docker.io/bitnamicharts/redis \
    --set auth.existingSecret=redis-password-secret \
    --set architecture=standalone
