# kube-prometheus

kube-prometheus kustomizations for AKS clusters.

``` bash
kustomize build https://github.com/juan-lee/kustomize-library/kube-prometheus | kubectl apply
--server-side -f -
```
