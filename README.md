# Setup Ingress NGINX

## Configure Ports and IP

Create `values.yaml`

```
controller:
  service:
    ports:
        http: 8080
        https: 8443
  loadBalancerIP: localhost
```

## Install

```
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm -n kube-system install --create-namespace ingress-nginx ingress-nginx/ingress-nginx -f values.yaml
```
