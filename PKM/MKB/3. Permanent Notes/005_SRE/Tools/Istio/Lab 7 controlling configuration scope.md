
By default Istio networking resources and services are visible to all services running in all namespaces that are part of the Istio service mesh. As you add more services to the mesh, the amount of configuration in the sidecar proxies increases dramatically which will grow the memory used by sidecar proxies accordingly. Thus this default behavior is not desired when you have more than a few services and not all of your services communicate to all other services.

The Sidecar resource is designed to solve this problem from the service consumer's perspective. This configuration allows you to configure the sidecar proxy that mediates inbound and outbound communication to the workload instance it is applicable to. The Export-To configuration allows service producers to declare the scope of the services to be exported. With both of these configurations, service owners can effectively control the scope of the sidecar configuration.

## Sidecar resource for service consumers

Let's declare services in the istioinaction namespace. Those services are allowed to reach out to the web-api service in the istioinaction namespace via the default sidecar resource below:

`apiVersion: networking.istio.io/v1beta1 kind: Sidecar metadata:   name: default  namespace: istioinaction spec:   egress:   - hosts:     - "./web-api.istioinaction.svc.cluster.local"     - "istio-system/*"     - "istio-ingress/*"`

Apply this resource:

`kubectl apply -f labs/07/default-sidecar-web-api.yaml -n istioinaction`

Let us reach the web-api service from the sleep pod in the istioinaction namespace:

`kubectl exec deploy/sleep -n istioinaction -- curl http://web-api.istioinaction:8080/`

You will get a 500 error code:

`Defaulting container name to sleep. {   "name": "web-api",   "uri": "/",   "type": "HTTP",   "ip_addresses": [     "192.168.1.168"   ],   "start_time": "2021-03-04T04:03:58.041278",   "end_time": "2021-03-04T04:03:58.044000",   "duration": "2.7219ms",   "upstream_calls": [     {       "uri": "http://recommendation:8080",       "code": 503,       "error": "Error processing upstream request: http://recommendation:8080/"     }   ],   "code": 500 }`

This is because the web-api pod's Sidecar doesn't know how to get to the recommendation service successfully. If you review the cluster output, you can see the web-api pod is not aware of the recommendation or purchase-history at all.

`istioctl pc cluster deploy/web-api.istioinaction`

You should get output like below with many services from the istio-system and istio-ingress namespaces and only the web-api service from the istioinaction namespace:

`SERVICE FQDN                                            PORT      SUBSET     DIRECTION     TYPE             DESTINATION RULE ... istio-ingressgateway.istio-ingress.svc.cluster.local    80        -          outbound      EDS istio-ingressgateway.istio-ingress.svc.cluster.local    443       -          outbound      EDS istio-ingressgateway.istio-ingress.svc.cluster.local    15012     -          outbound      EDS istio-ingressgateway.istio-ingress.svc.cluster.local    15021     -          outbound      EDS istio-ingressgateway.istio-ingress.svc.cluster.local    15443     -          outbound      EDS istiod-1-16.istio-system.svc.cluster.local             443       -          outbound      EDS istiod-1-16.istio-system.svc.cluster.local             15010     -          outbound      EDS istiod-1-16.istio-system.svc.cluster.local             15012     -          outbound      EDS istiod-1-16.istio-system.svc.cluster.local             15014     -          outbound      EDS istiod.istio-system.svc.cluster.local                   443       -          outbound      EDS istiod.istio-system.svc.cluster.local                   15010     -          outbound      EDS istiod.istio-system.svc.cluster.local                   15012     -          outbound      EDS istiod.istio-system.svc.cluster.local                   15014     -          outbound      EDS kiali.istio-system.svc.cluster.local                    9090      -          outbound      EDS kiali.istio-system.svc.cluster.local                    20001     -          outbound      EDS web-api.istioinaction.svc.cluster.local                 8080      -          outbound      EDS ...`

Let's modify the sidecar resource to allow all services within the same namespace:

`apiVersion: networking.istio.io/v1beta1 kind: Sidecar metadata:   name: default  namespace: istioinaction spec:   egress:   - hosts:     - "./*"     - "istio-system/*"     - "istio-ingress/*"`

Apply this default-sidecar-allows-all-egress.yaml file to your cluster:

`kubectl apply -f labs/07/default-sidecar-allows-all-egress.yaml -n istioinaction`

Curl the web-api from the sleep pod in the istioinaction namespace:

`kubectl exec deploy/sleep -n istioinaction -- curl http://web-api.istioinaction:8080/`

You should see 200 code from the recommendation service and purchase-history services:

`Defaulting container name to sleep. {   "name": "web-api",   "uri": "/",   "type": "HTTP",   "ip_addresses": [     "192.168.1.168"   ],   "start_time": "2021-03-03T20:54:04.694092",   "end_time": "2021-03-03T20:54:04.706462",   "duration": "12.3708ms",   "body": "Hello From Web API",   "upstream_calls": [     {       "name": "recommendation",       "uri": "http://recommendation:8080",       "type": "HTTP",       "ip_addresses": [         "192.168.1.169"       ],       "start_time": "2021-03-03T20:54:04.698750",       "end_time": "2021-03-03T20:54:04.705537",       "duration": "6.7868ms",       "body": "Hello From Recommendations!",       "upstream_calls": [         {           "name": "purchase-history-v1",           "uri": "http://purchase-history:8080",           "type": "HTTP",           "ip_addresses": [             "192.168.1.175"           ],           "start_time": "2021-03-03T20:54:04.704524",           "end_time": "2021-03-03T20:54:04.704611",           "duration": "88µs",           "body": "Hello From Purchase History (v1)!",           "code": 200         }       ],       "code": 200     }   ],   "code": 200 }`

If you view the cluster configuration for the web-api pod in the istioinaction namespace:

`istioctl pc cluster deploy/web-api.istioinaction`

You should see the recommendation, purchase-history and sleep service from the istioinaction namespace in the output:

`SERVICE FQDN                                            PORT      SUBSET     DIRECTION     TYPE             DESTINATION RULE ... purchase-history.istioinaction.svc.cluster.local        8080      -          outbound      EDS recommendation.istioinaction.svc.cluster.local          8080      -          outbound      EDS sleep.istioinaction.svc.cluster.local                   80        -          outbound      EDS web-api.istioinaction.svc.cluster.local                 8080      -          outbound      EDS ...`

Restart the sleep pod to start the sidecar proxy:

`k rollout restart deployment sleep`

Now reach the web-api service from the sleep pod in the default namespace:

`kubectl exec deploy/sleep -n default -- curl http://web-api.istioinaction:8080/`

You will get a 200 status code because the sleep pod in the default namespace doesn't have any sidecar resource thus can see all the configuration for the web-api service in istioinaction.

In summary, the Sidecar resource can be used per namespace as shown above, or by using using a label selector per workload or globally. It is recommended to enable it per namespace or workload first before enabling it globally. The Sidecar resource controls the visibility of configurations and what gets pushed to the sidecar proxy. Further, the Sidecar resource should NOT be used as security enforcement to prevent service A from reaching service B. Istio authorization policy (or network policy for layer 3/4 traffic) should be used instead to enforce the security boundry.

## export-To configuration for service producers

Service owners can apply export-To in Virtual Service, Destination Rule and Service Entry resources to define a list of namespaces that the Istio networking resources can be applied to. Further, service owners can declare their services' visibility via the networking.istio.io/exportTo annotation. By default, if no export-To exists for these Istio resources or users' services, they are made available to all namespaces in the mesh. However, it is best practice to trim the unnecessary proxy configurations. For example, as the service owner for the recommendation service, you may want to allow the web-api service to be available only from the istioinaction, istio-system and istio-ingress namespaces.

Now send some traffic through the ingress gateway:

`curl --cacert ./labs/04/certs/ca/root-ca.crt -H "Host: istioinaction.io" https://istioinaction.io:443 --resolve istioinaction.io:443:$GATEWAY_IP`

You should get 200 status code because in lab04, you have configured the gateway resource and virtual service resource in the istioinaction namespace, along with the istioinaction-cert secret in the istio-ingress namespace.

Now, let us try creating the web-api-gateway resource in the istio-ingress namespace, and continue to have the web-api-gw-vs virtual service resource in the istioinaction namespace and explore exportTo on this virtual service resource.

First, delete the web-api-gateway gateway resource in the istioinaction namespace:

`kubectl delete gw web-api-gateway -n istioinaction`

Create the web-api-gateway gateway resource in the istio-ingress namespace instead:

`kubectl apply -f labs/07/web-api-gw-https-istiosystem.yaml -n istio-ingress`

Update the web-api-gw-vs virtual service resource in the istioinaction namespace, under the `gateways` section to refer to the `web-api-gateway` in the `istio-ingress` namespace.

`apiVersion: networking.istio.io/v1beta1 kind: VirtualService metadata:   name: web-api-gw-vs   namespace: istioinaction spec:   hosts:   - "istioinaction.io"   gateways:   - istio-ingress/web-api-gateway   http:   - route:     - destination:         host: web-api.istioinaction.svc.cluster.local         port:           number: 8080`

Apply the virtual service resource:

`kubectl apply -f labs/07/web-api-gw-vs.yaml -n istioinaction`

Send some traffic to the web-api service through the istio ingress gateway via https:

`curl -i --cacert ./labs/04/certs/ca/root-ca.crt -H "Host: istioinaction.io" https://istioinaction.io:443 --resolve istioinaction.io:443:$GATEWAY_IP`

You should continue to get a 200 status code.

Check the route configuration for istio-ingressgateway in the istio-system namespace:

`istioctl pc route deploy/istio-ingressgateway.istio-ingress`

The route output shows the expected web-api-gw-vs.istioinaction for the web-api-gateway for both port 443 and port 8080:

`NAME                                              DOMAINS              MATCH                  VIRTUAL SERVICE http.8080                                         istioinaction.io     /*                     web-api-gw-vs.istioinaction https.443.https.web-api-gateway.istio-ingress     istioinaction.io     /*                     web-api-gw-vs.istioinaction                                                   *                    /healthz/ready*                                                   *                    /stats/prometheus*`

Now as web-api's service producer, you want to limit its virtual service scope to within the current namespace, with the exportTo configuration:

`apiVersion: networking.istio.io/v1beta1 kind: VirtualService metadata:   name: web-api-gw-vs   namespace: istioinaction spec:   hosts:   - "istioinaction.io"   gateways:   - istio-ingress/web-api-gateway   exportTo:   - "."   http:   - route:     - destination:         host: web-api.istioinaction.svc.cluster.local         port:           number: 8080`

Apply the updated virtual service resource:

`kubectl apply -f labs/07/web-api-gw-vs-exportto.yaml -n istioinaction`

Send some traffic to web-api through the istio ingress gateway via https:

`curl -i --cacert ./labs/04/certs/ca/root-ca.crt -H "Host: istioinaction.io" https://istioinaction.io:443 --resolve istioinaction.io:443:$GATEWAY_IP`

Because istio ingress gateway doesn't know how to route to the web-api service in the istioinaction namespace, you will get an empty reply instead. Confirm the route configuration for istio-ingressgateway in the istio-ingress namespace:

`istioctl pc route deploy/istio-ingressgateway.istio-ingress`

The route output shows 404 for the web-api-gateway's virtual service:

`NAME                                              DOMAINS     MATCH                  VIRTUAL SERVICE http.8080                                         *           /*                     404 https.443.https.web-api-gateway.istio-ingress     *           /*                     404                                                   *           /healthz/ready*                                                   *           /stats/prometheus*`

Let's undo the exportTo configuration in the virtual service resource:

`kubectl apply -f labs/07/web-api-gw-vs.yaml -n istioinaction`

Confirm you can send some traffic to web-api service via istio-ingressgateway and get a 200 status code. Check the clusters for the istio-ingressgateway:

`istioctl pc cluster deploy/istio-ingressgateway.istio-ingress`

You'll see web-api.istioinaction is in the list of services:

`SERVICE FQDN                                PORT     SUBSET     DIRECTION     TYPE           DESTINATION RULE BlackHoleCluster                            -        -          -             STATIC agent                                       -        -          -             STATIC prometheus_stats                            -        -          -             STATIC sds-grpc                                    -        -          -             STATIC web-api.istioinaction.svc.cluster.local     8080     -          outbound      EDS xds-grpc                                    -        -          -             STATIC zipkin                                      -        -          -             STRICT_DNS`

Add the networking.istio.io/exportTo annotation to the web-api service to specify the service is only exported to the deployed namespace (e.g. istioinaction):

`apiVersion: v1 kind: Service metadata:   name: web-api   annotations:     networking.istio.io/exportTo: "." spec:   selector:     app: web-api   ports:   - name: http    protocol: TCP    port: 8080     targetPort: 8080`

Deploy the change to the web-api service:

`kubectl apply -f labs/07/web-api-service-exportto.yaml -n istioinaction`

Do you think you will be able to reach web-api via istio ingressgateway? You won't because the web-api service declares it is only exposed to the istioinaction namespace. Check the clusters for the istio-ingressgateway:

`istioctl pc cluster deploy/istio-ingressgateway.istio-ingress`

You'll NOT see web-api.istioinaction is in the list of services:

`SERVICE FQDN         PORT     SUBSET     DIRECTION     TYPE           DESTINATION RULE BlackHoleCluster     -        -          -             STATIC agent                -        -          -             STATIC prometheus_stats     -        -          -             STATIC sds-grpc             -        -          -             STATIC xds-grpc             -        -          -             STATIC zipkin               -        -          -             STRICT_DNS`

Let us undo the annotation so that you can continue to reach the web-api service via istio-ingressgateway:

`kubectl apply -f labs/07/web-api-service.yaml -n istioinaction`

## Virtual service resource merging

Istio supports simple virtual service resource merging for the same host when there are no conflicts. This is helpful when each service owner owns the virtual service resource on his/her own. Let us explore this with the helloworld sample from Istio along with our web-api service.

Deploy the helloworld sample in the istioinaction namespace:

`kubectl apply -f labs/07/helloworld.yaml -n istioinaction`

You should see the helloworld pods reach running in a few seconds:

`kubectl get po -n istioinaction`

Deploy the helloworld virtual service. This virtual service has some changes from the default one shipped from Istio: The hosts and gateways values refer to the same host and gateway as the web-api service.

`apiVersion: networking.istio.io/v1beta1 kind: VirtualService metadata:   name: helloworld spec:   hosts:   - "istioinaction.io"   gateways:   - istio-ingress/web-api-gateway   http:   - match:     - uri:         exact: /hello    route:     - destination:         host: helloworld        port:           number: 5000`

Deploy this helloworld virtual service in the istioinaction namespace

`kubectl apply -f labs/07/helloworld-vs.yaml -n istioinaction`

Send some http traffic to the helloworld service via the istio-ingressgateway:

`curl -i -H "Host: istioinaction.io" http://istioinaction.io:80/hello --resolve istioinaction.io:80:$GATEWAY_IP`

Also send some https traffic to the helloworld service via the istio-ingressgateway:

`curl -i --cacert ./labs/04/certs/ca/root-ca.crt -H "Host: istioinaction.io" https://istioinaction.io:443/hello --resolve istioinaction.io:443:$GATEWAY_IP`

You should receive some hello message like below for both of these curl commands:

`Hello version: v1, instance: helloworld-v1-776f57d5f6-29xdw`

Send some https traffic to the web-api service via the istio-ingressgateway:

`curl -i --cacert ./labs/04/certs/ca/root-ca.crt -H "Host: istioinaction.io" https://istioinaction.io:443 --resolve istioinaction.io:443:$GATEWAY_IP`

You should see the 200 status code as before. This is because Istiod successfully merged the virtual services resources (helloworld-vs.yaml & web-api-gw-vs.yaml) automatically for you knowing they are using the same host name. Examine the routes configuration for istio-ingressgateway:

`istioctl pc routes deploy/istio-ingressgateway -n istio-ingress`

From the output, you can see the routes are merged for the istioinaction.io host for both https.443 and http.80.

`NAME                                              DOMAINS              MATCH                  VIRTUAL SERVICE http.8080                                         istioinaction.io     /hello                 helloworld.istioinaction http.8080                                         istioinaction.io     /*                     web-api-gw-vs.istioinaction https.443.https.web-api-gateway.istio-ingress     istioinaction.io     /hello                 helloworld.istioinaction https.443.https.web-api-gateway.istio-ingress     istioinaction.io     /*                     web-api-gw-vs.istioinaction                                                   *                    /healthz/ready*                                                   *                    /stats/prometheus*`

## Next Lab

As you explore your services in Istio, things may not go smoothly. In the next lab, we will explore how to debug your services or Istio resources in your service mesh.