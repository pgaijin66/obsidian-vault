
Adding l7 auth policies

L4 policies are useful but may not be sufficient for your needs.

For example, you’ll be able to send any request to the `web-api` service from the `sleep` pod while you may only want to allow requests with the `GET` method.

In order to have any L7 policy enforced for the `web-api` service, you’ll need to deploy a waypoint proxy for the service account used by the `web-api` pod.

![waypoint proxy](https://istio-ambient-mesh.s3.amazonaws.com/images/steps/l7-authorization-policies/waypoint-proxy.png)

The Kubernetes gateway API is used to deploy a waypoint proxy for a given service account or the namespace.

The example below demonstrates how to ask Istio to deploy a waypoint proxy for the `web-api` service account.

`kubectl apply -f - <<EOF apiVersion: gateway.networking.k8s.io/v1alpha2 kind: Gateway metadata:   name: web-api  namespace: default  annotations:    istio.io/service-account: web-api spec:   gatewayClassName: istio-mesh EOF`

Let's check that the waypoint proxy has been deployed correctly:

`kubectl get pods -l ambient-type=waypoint`

Here is the expected output:

`NAME                                      READY   STATUS    RESTARTS   AGE web-api-waypoint-proxy-7bbfdc784c-5vg9n   1/1     Running   0          69s`

Now, let's allow the `sleep` pod to only send `GET` requests to the `web-api` service:

`kubectl apply -f - <<EOF apiVersion: security.istio.io/v1beta1 kind: AuthorizationPolicy metadata:   name: "web-api-rbac"  namespace: default spec:   selector:    matchLabels:      app: web-api  action: ALLOW  rules:  - from:    - source:        principals: ["cluster.local/ns/default/sa/sleep","cluster.local/ns/istio-system/sa/istio-ingressgateway-service-account"]    to:    - operation:        methods: ["GET"] EOF`

You should still be able to access the `web-api` application from the `sleep` pod.

`kubectl exec deploy/sleep -- curl http://web-api:8080/`

But if you try to send a `DELETE` request, it will be denied:

`kubectl exec deploy/sleep -- curl http://web-api:8080/ -X DELETE`

You should get the following output:

`RBAC: access denied`

Note that a deny at L7 is also providing a better user experience (with an explicit error message and a 403 response code).


Add l7 observability

With waypoint proxy deployed for the `web-api` service, you automatically get L7 metrics for this service.

For example, you can view the 403 response code from the `web-api` service’s waypoint proxy’s `/stats/prometheus` endpoint:

`kubectl exec deploy/web-api-waypoint-proxy -- curl http://localhost:15020/stats/prometheus | grep istio_requests_total`

You should get something similar to this:

`istio_requests_total{response_code="403",reporter="destination",source_workload="sleep",source_workload_namespace="default",source_principal="spiffe://cluster.local/ns/default/sa/sleep",source_app="unknown",source_version="unknown",source_cluster="Kubernetes",destination_workload="web-api",destination_workload_namespace="default",destination_principal="spiffe://cluster.local/ns/default/sa/web-api",destination_app="unknown",destination_version="unknown",destination_service="web-api.default.svc.cluster.local",destination_service_name="web-api",destination_service_namespace="default",destination_cluster="Kubernetes",request_protocol="http",response_flags="-",grpc_response_status="",connection_security_policy="mutual_tls",source_canonical_service="sleep",destination_canonical_service="web-api",source_canonical_revision="latest",destination_canonical_revision="v1"} 2`


Add fault injection

It is difficult to get service timeouts and circuit-breaker configured properly in a distributed microservice application.

Istio makes it easier to get these settings correct by enabling you to inject faults into your service requests without the need to modify your code.

Inject some fault (such as 5 second delay) to the `web-api` service to test resiliency:

`kubectl apply -f - <<EOF apiVersion: networking.istio.io/v1beta1 kind: VirtualService metadata:   name: web-api-gw-vs spec:   hosts:  - "istioexplained.io"  gateways:  - web-api-gateway  http:  - fault:      delay:        fixedDelay: 5s        percentage:          value: 100    match:    - headers:        user:          exact: Amy    route:    - destination:        host: web-api.default.svc.cluster.local        port:          number: 8080  - route:    - destination:        host: web-api.default.svc.cluster.local        port:          number: 8080 EOF`

If you try to access the `web-api` service through the ingress gateway, with the header `user: Amy`, you'll notice a 5 seconds delay:

`curl -H "Host: istioexplained.io" -H "user: Amy" "http://${GATEWAY_IP}/"`



Add traffic shifting

A canary test is often performed to ensure the new version of a service not only functions properly but also doesn’t cause a degradation in performance or reliability.

For example, if there is a new version (v2) of the `purchase-history` service, you may want to gradually roll out the `v2` version of the service by sending only 10% of the traffic to it.

Deploy the `v2` version of `purchase-history` service:

`kubectl apply -f data/steps/traffic-shifting/purchase-history-v2.yaml`

In order to apply traffic shift on the `purchase-history` service, you’ll need to deploy a waypoint proxy for the service, similar to deploying a waypoint proxy via a Kubernetes gateway resource for the `web-api` service earlier.

`kubectl apply -f - <<EOF apiVersion: gateway.networking.k8s.io/v1alpha2 kind: Gateway metadata:   name: purchase-history  annotations:    istio.io/service-account: purchase-history spec:   gatewayClassName: istio-mesh EOF`

Apply a `VirtualService` resource that configures the traffic shifting:

`kubectl apply -f - <<EOF apiVersion: networking.istio.io/v1beta1 kind: VirtualService metadata:   name: purchase-history-vs spec:   hosts:  - purchase-history.default.svc.cluster.local  http:  - route:    - destination:        host: purchase-history.default.svc.cluster.local        subset: v1        port:          number: 8080      weight: 90    - destination:        host: purchase-history.default.svc.cluster.local        subset: v2        port:          number: 8080      weight: 10 EOF`

The subset referred in the above `VirtualService` must be defined in a `DestinationRule` resource so that Istio knows which subsets map to what labels for the `purchase-history` host.

`kubectl apply -f - <<EOF apiVersion: networking.istio.io/v1beta1 kind: DestinationRule metadata:   name: purchase-history-dr spec:   host: purchase-history.default.svc.cluster.local  subsets:  - name: v1    labels:      version: v1  - name: v2    labels:      version: v2 EOF`

You can confirm that around 10% of the traffic is sent to the `v2` version of the `purchase-history` service by running the following command:

`kubectl exec deploy/sleep -- sh -c 'for i in $(seq 1 100); do curl -s http://web-api:8080/; done | grep -c purchase-history-v2'`

This command has sent a total of 100 requests, so you should get around 10 requests sent to v2.