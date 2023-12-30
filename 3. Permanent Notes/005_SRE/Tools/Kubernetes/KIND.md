

https://jamesdefabia.github.io/docs/getting-started-guides/docker/

Installation
`brew install kind`

Create a new cluster
`kind create cluster --name cluste-name`

See cluster
`kind get clusters`

List local k8s contexts
`kubectl config get-contexts`

Choose context
`kubectl config use-context kind-istio-testing`

Delete cluster
`kind delete cluster --name istio-testing`


Setup dashboard
`kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml

Verify dashboard
`kubectl get pods -n k8s-dashboard`

Create SA and CRB to provide admin access
```
kubectl create serviceaccount -n kubernetes-dashboard admin-user
kubectl create clusterrolebinding -n kubernetes-dashboard admin-user --clusterrole cluster-admin --serviceaccount=kubernetes-dashboard:admin-user

```

Login to dashboard using Bearer token
```
token=$(kubectl -n kubernetes-dashboard create token admin-user)
```

`echo $token`

```
kubectl proxy
```
