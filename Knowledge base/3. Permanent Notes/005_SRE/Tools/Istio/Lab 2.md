
### Add services to ambient

# asd


Adding services to Ambient is very simple.

You just need to add the `istio.io/dataplane-mode=ambient` label to your namespace to have all the corresponding pods managed by Ambient.

`kubectl label namespace default istio.io/dataplane-mode=ambient`

Now, take a look at the logs of the Istio CNI DaemonSet using the following command:

`kubectl -n istio-system logs -l k8s-app=istio-cni-node`

You should see something similar to this:

`2022-08-31T09:05:23.058515Z	info	ambient	Adding pod 'recommendation-bb5cbdc9d-mxhhh/default' (f2f10cd4-6c43-4bcd-a5a3-78e42e8c96a0) to ipset 2022-08-31T09:05:23.061199Z	info	ambient	Adding route for recommendation-bb5cbdc9d-mxhhh/default: [table 100 10.101.2.4/32 via 192.168.126.2 dev istioin src 10.101.2.1] 2022-08-31T09:05:23.062455Z	info	ambient	Adding pod 'web-api-7c9755c6d-nhwn6/default' (8a1de86f-29ee-4b5c-a1a6-0c4e80c39113) to ipset 2022-08-31T09:05:23.064030Z	info	ambient	Adding route for web-api-7c9755c6d-nhwn6/default: [table 100 10.101.2.5/32 via 192.168.126.2 dev istioin src 10.101.2.1] 2022-08-31T09:05:23.064945Z	info	ambient	Adding pod 'notsleep-5564c6dd94-fnv6m/default' (f61dc682-5b37-4f1e-b235-1c40c945dc26) to ipset 2022-08-31T09:05:23.066424Z	info	ambient	Adding route for notsleep-5564c6dd94-fnv6m/default: [table 100 10.101.2.6/32 via 192.168.126.2 dev istioin src 10.101.2.1] 2022-08-31T09:05:23.067258Z	info	ambient	Adding pod 'sleep-84585fd6cc-k2fxb/default' (23c8f5f9-b638-48d5-b853-6efa6c73ebff) to ipset 2022-08-31T09:05:23.068780Z	info	ambient	Adding route for sleep-84585fd6cc-k2fxb/default: [table 100 10.101.2.7/32 via 192.168.126.2 dev istioin src 10.101.2.1]`

The CNI DaemonSet on each node constantly watches for pods (on the same node) that are running in namespaces with the `istio.io/dataplane-mode=ambient` label.

Whenever a pod with the matching namespace label is detected, the CNI DaemonSet checks if the pod opts out of ambient. If so, the CNI DaemonSet ignores the pod. If not, the CNI DaemonSet applies iptables redirect rules to redirect all the outgoing and incoming traffic from/to the pod to the ztunnel running on the same node.

Let us walk through a simple example where the `sleep` (client) pod calls the `web-api` (server) service via http on port 8080. When the `sleep` pod makes the request, it will be redirected to ztunnel running on the same node as the `sleep` pod.

This ztunnel will then establish an HTTP/2 CONNECT tunnel with the ztunnel running on the same node as the `web-api` pod and send the request as byte streams through the tunnel.

The target ztunnel will finally forward the request to the `web-api` pod running on the same node.

![Application with Istio](https://istio-ambient-mesh.s3.amazonaws.com/images/steps/add-services-to-ambient/application-with-istio.png)

Similar to sidecar, each service account will be assigned its own identity and client key/certificate pair.

Client ztunnel will establish the mTLS connection using the client’s key/certificate pair on behalf of the client pod and server ztunnel will terminate the mTLS connection and forward the request to the server pod.

By adding your client and server pods to ambient, it enables your client pod to communicate with the server pod with encrypted traffic using cryptographic identity automatically, without any code change or sidecar injection to the client or server.

Further, you automatically gather your pods’ key Layer 4 (L4) metrics, such as TCP bytes sent.

Let's generate some traffic:

`kubectl exec deploy/sleep -- sh -c 'for i in $(seq 1 100); do curl -s -I http://web-api:8080/; done'`

You can confirm ztunnels are mediating the traffic between client and server by reviewing the ztunnel access logs.

`kubectl -n istio-system logs -l app=ztunnel | grep '^\['`

You should get something like this:

`[2022-08-31T11:28:06.270Z] "- - -" 0 - - - "-" 1720 439 4 - "-" "-" "-" "-" "10.101.1.4:15008" outbound_tunnel_clus_spiffe://cluster.local/ns/default/sa/recommendation 10.101.2.3:57192 10.101.1.4:8080 envoy://internal_client_address/ - - outbound tunnel [2022-08-31T11:28:06.269Z] "CONNECT - HTTP/2" 200 - via_upstream - "-" 1718 824 5 - "-" "-" "5329e14b-506a-4fe4-be16-3fae92677fb6" "10.101.2.4:8080" "10.101.2.4:8080" virtual_inbound 10.101.2.3:56737 10.101.2.4:15088 10.101.2.3:44612 - - inbound hcm [2022-08-31T11:28:06.270Z] "- - -" 0 - - - "-" 1720 439 4 - "-" "-" "-" "-" "envoy://outbound_tunnel_lis_spiffe://cluster.local/ns/default/sa/recommendation/10.101.1.4:8080" spiffe://cluster.local/ns/default/sa/recommendation_to_http_purchase-history.default.svc.cluster.local_outbound_internal envoy://internal_client_address/ 10.1.227.77:8080 10.101.2.4:50968 - - outbound capture listener [2022-08-31T11:28:06.270Z] "- - -" 0 - - - "-" 1720 439 4 - "-" "-" "-" "-" "envoy://outbound_tunnel_lis_spiffe://cluster.local/ns/default/sa/recommendation/10.101.1.4:8080" spiffe://cluster.local/ns/default/sa/recommendation_to_http_purchase-history.default.svc.cluster.local_outbound_internal envoy://internal_client_address/ 10.1.227.77:8080 10.101.2.4:50968 - - capture outbound (no waypoint proxy) [2022-08-31T11:28:06.266Z] "- - -" 0 - - - "-" 1718 824 8 - "-" "-" "-" "-" "10.101.2.4:15088" outbound_tunnel_clus_spiffe://cluster.local/ns/default/sa/web-api 10.101.2.3:44612 10.101.2.4:8080 envoy://internal_client_address/ - - outbound tunnel [2022-08-31T11:28:06.266Z] "- - -" 0 - - - "-" 1718 824 8 - "-" "-" "-" "-" "envoy://outbound_tunnel_lis_spiffe://cluster.local/ns/default/sa/web-api/10.101.2.4:8080" spiffe://cluster.local/ns/default/sa/web-api_to_http_recommendation.default.svc.cluster.local_outbound_internal envoy://internal_client_address/ 10.1.78.119:8080 10.101.2.5:40800 - - outbound capture listener [2022-08-31T11:28:06.266Z] "- - -" 0 - - - "-" 1718 824 8 - "-" "-" "-" "-" "envoy://outbound_tunnel_lis_spiffe://cluster.local/ns/default/sa/web-api/10.101.2.4:8080" spiffe://cluster.local/ns/default/sa/web-api_to_http_recommendation.default.svc.cluster.local_outbound_internal envoy://internal_client_address/ 10.1.78.119:8080 10.101.2.5:40800 - - capture outbound (no waypoint proxy) [2022-08-31T11:28:06.273Z] "CONNECT - HTTP/2" 200 - via_upstream - "-" 1720 439 0 - "-" "-" "ae613a76-8ab1-4f7a-9b6c-1c4612b0a475" "10.101.1.4:8080" "10.101.1.4:8080" virtual_inbound 10.101.2.3:36195 10.101.1.4:15008 10.101.2.3:57192 - - inbound hcm`

You may also notice `capture outbound (no waypoint proxy)` in the log. Through the istiod control plane, ztunnel knows whether there is a waypoint proxy on the server side. We’ll explain this soon when we introduce some policies !

What you've seen so far is that services can be added to the Mesh without restarting any Pod.

By adding services in the mesh, you get data encrypted automatically (between Kubernetes nodes) and observability at the L4 level.



### Add l4 auth policies

While it is great to have mTLS among all my service connections, to fulfill a zero trust architecture, you’ll likely want to deny all accesses and only grant certain accesses explicitly.

And now that services have been added to the mesh, you can easily apply authorization at the L4 level.

Deploy a deny all policy for everything in the default namespace:

`kubectl apply -f - <<EOF apiVersion: security.istio.io/v1beta1 kind: AuthorizationPolicy metadata:   name: allow-nothing  namespace: default spec:   {} EOF`

You shouldn't be able to access the `web-api` application anymore.

`curl -H "Host: istioexplained.io" "http://${GATEWAY_IP}/"`

Now, let's create the different `AuthorizationPolicy` resources to only allow the communications needed.

`kubectl apply -f - <<EOF apiVersion: security.istio.io/v1beta1 kind: AuthorizationPolicy metadata:   name: "web-api-rbac"  namespace: default spec:   selector:    matchLabels:      app: web-api  action: ALLOW  rules:  - from:    - source:        principals: ["cluster.local/ns/default/sa/sleep","cluster.local/ns/istio-system/sa/istio-ingressgateway-service-account"] --- apiVersion: security.istio.io/v1beta1 kind: AuthorizationPolicy metadata:   name: "recommendation-rbac"  namespace: default spec:   selector:    matchLabels:      app: recommendation  action: ALLOW  rules:  - from:    - source:        principals: ["cluster.local/ns/default/sa/web-api"] --- apiVersion: security.istio.io/v1beta1 kind: AuthorizationPolicy metadata:   name: "purchase-history-rbac"  namespace: default spec:   selector:    matchLabels:      app: purchase-history  action: ALLOW  rules:  - from:    - source:        principals: ["cluster.local/ns/default/sa/recommendation"] EOF`

You should now be able to access the `web-api` application again.

`curl -H "Host: istioexplained.io" "http://${GATEWAY_IP}/"`

But if you try to access it from an unauthorized Pod, access will be denied:

`kubectl exec deploy/notsleep -- curl http://web-api:8080/`

- 
tags
#hello 