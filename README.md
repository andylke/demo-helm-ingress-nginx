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

## Create Echo Ingress Test

Create `echo.yaml`

```
kind: Pod
apiVersion: v1
metadata:
  name: echo
  labels:
    app: echo
spec:
  containers:
    - name: echo
      image: hashicorp/http-echo
      args:
        - "-text=echo"
---
kind: Service
apiVersion: v1
metadata:
  name: echo
spec:
  selector:
    app: echo
  ports:
    - port: 5678 # Default port for image
---
apiVersion: networking.k8s.io/v1
kind: Ingressmetadata:
  name: echo
  annotations:
    ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
    - http:
        paths:
          - path: /echo
            pathType: ImplementationSpecific
            backend:
              service:
                name: echo-service
                port:
                  number: 5678
```

Apply
`kubectl apply -f echo.yaml`

Verify exposed URL
`curl http://example.com:8080/echo`

```
StatusCode        : 200
StatusDescription : OK
Content           : echo
```

## To delete Echo Ingress Test

`kubectl delete -f echo.yaml`
