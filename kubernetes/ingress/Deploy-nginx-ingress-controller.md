# Deploying NGINX Ingress Controller

## Install NGINX Ingress Controller using Helm Chart

To install NGINX Ingress Controller using Helm, first add the NGINX Helm repository:

```sh
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### Create NGINX Ingress Namespace

```sh
kubectl create namespace ingress-nginx
```

### Install the Controller with Default Values

```sh
helm install my-ingress-nginx ingress-nginx/ingress-nginx --namespace ingress-nginx
```

### Watch the Load Balancer Status

```sh
kubectl --namespace ingress-nginx get services -o wide -w ingress-nginx-controller
```

---

## Troubleshooting Installation Errors

### Error: Kubernetes Cluster Unreachable

```sh
Error: INSTALLATION FAILED: Kubernetes cluster unreachable: Get "http://localhost:8080/version": dial tcp 127.0.0.1:8080: connect: connection refused
```

#### Solution:

1. **Option 1:** Export the `KUBECONFIG` environment variable:
   ```sh
   export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
   ```

2. **Option 2:** Copy the config to your home directory:
   ```sh
   mkdir -p ~/.kube
   sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
   sudo chown ubuntu:ubuntu ~/.kube/config
   ```

### Error: Permission Denied on Kubeconfig

```sh
Error: INSTALLATION FAILED: Kubernetes cluster unreachable: error loading config file "/etc/rancher/k3s/k3s.yaml": open /etc/rancher/k3s/k3s.yaml: permission denied
```

#### Solution:

Check the permissions of the config file:
```sh
sudo ls -l /etc/rancher/k3s/k3s.yaml
```

If it is owned by `root`, unset the `KUBECONFIG` variable to use the local user config:
```sh
unset KUBECONFIG
```

Verify the local config is readable:
```sh
ls -l ~/.kube/config
```

After fixing permissions, retry the installation:
```sh
helm install my-ingress-nginx ingress-nginx/ingress-nginx --namespace ingress-nginx
```

### Successful Installation Output

```
NAME: my-ingress-nginx
LAST DEPLOYED: Wed Jan 29 13:43:33 2025
NAMESPACE: ingress-nginx
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

---

## Example Ingress Resource

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example
  namespace: foo
spec:
  ingressClassName: nginx
  rules:
    - host: www.example.com
      http:
        paths:
          - pathType: Prefix
            backend:
              service:
                name: exampleService
                port:
                  number: 80
            path: /
  tls:
    - hosts:
      - www.example.com
      secretName: example-tls
```

### TLS Secret Example

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: example-tls
  namespace: foo
data:
  tls.crt: <base64 encoded cert>
  tls.key: <base64 encoded key>
type: kubernetes.io/tls
```

