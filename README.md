# Lokomotive Components Lifecycle using Flux

- Download flux binary.
- Install flux on the cluster by running the following command:

```bash
kubectl create ns flux-system
kubectl -n flux-system create rolebinding privileged-psp --clusterrole=privileged-psp --group=system:serviceaccounts:flux-system
flux install --version=latest --namespace=flux-system --components=source-controller,helm-controller
```

- Install Lokomotive git repository:

```bash
kubectl apply -f configs/lokomotive.yaml
```

- Install individual components:

```bash
kubectl apply -f configs/hr-<component name>.yaml
```

- For `external-dns` component run the following command:

```bash
export AWS_ACCESS_KEY_ID=""
export AWS_SECRET_ACCESS_KEY=""
```

```yaml
echo "
apiVersion: v1
kind: Secret
metadata:
  name: external-dns-values
  namespace: flux-system
stringData:
  values: |
    provider: aws
    sources:
    - ingress
    - contour-httpproxy
    aws:
      credentials:
        secretKey: $AWS_ACCESS_KEY_ID
        accessKey: $AWS_SECRET_ACCESS_KEY
      zoneType: public
    txtOwnerId: suraj-flux-cluster
    policy: sync
    replicas: 1
" | kubectl apply -f -
```
