
**Deploy kind**

Set the context environment variable:

`export CLUSTER1=cluster1`

Run the following commands to deploy a multi node Kubernetes cluster using [KinD](https://kind.sigs.k8s.io/):

`data/steps/deploy-kind-cluster/deploy-multi.sh 1 cluster1`

Then run the following commands to wait for all the Pods to be ready:

`data/steps/deploy-kind-cluster/check.sh cluster1`

**Note:** If you run the `check.sh` script immediately after the `deploy.sh` script, you may see a jsonpath error. If that happens, simply wait a few seconds and try again.


Deploy istio

Download `istioctl` for ambient:

`wget https://storage.googleapis.com/istio-build/dev/0.0.0-ambient.191fe680b52c1754ee72a06b3e0d3f9d116f2e82/istioctl-0.0.0-ambient.191fe680b52c1754ee72a06b3e0d3f9d116f2e82-linux-amd64.tar.gz`

Uncompress the archive:

`tar zxvf istioctl*.tar.gz`

The Ambient profile is designed to help you get started with Ambient easily.

Install Istio with the ambient profile on your Kubernetes cluster.

`./istioctl install -y --set profile=ambient`

After running the above command, you’ll get the following output that indicates these four components are installed successfully!

`✔ Istio core installed ✔ Istiod installed ✔ Ingress gateways installed ✔ CNI installed ✔ Installation complete Making this installation the default for injection and validation.`

By default, the Ambient profile has the Istio core, Istiod, ingress gateway, ztunnel and CNI plugin enabled. The Istio CNI plugin is responsible for detecting which application pods are part of ambient and configuring the traffic redirection between them and the ztunnel.

Run the following command to check the Pods deployed in the `istio-system` namespace:

`kubectl -n istio-system get pods`

Here is the expected output:

`NAME                                    READY   STATUS    RESTARTS   AGE istio-cni-node-djhj5                    1/1     Running   0          11m istio-cni-node-k95w7                    1/1     Running   0          11m istio-cni-node-l6hh7                    1/1     Running   0          11m istio-cni-node-t7zd7                    1/1     Running   0          11m istio-ingressgateway-5dc9759c74-6kvqk   1/1     Running   0          11m istiod-c7f87d9dc-9jxhw                  1/1     Running   0          11m ztunnel-6k2s6                            1/1     Running   0          11m ztunnel-cmmdh                            1/1     Running   0          11m ztunnel-lwq2v                            1/1     Running   0          11m ztunnel-nj8h2                            1/1     Running   0          11m`

You’ll notice the following pods are deployed in the `istio-system` namespace with the default Ambient profile.


Deploy demo services


We're going to deploy the `web-api`, `recommendation`, and `purchase-history` services built using the [fake service](https://github.com/nicholasjackson/fake-service).

The `web-api` service calls the `recommendation` service via HTTP, and the `recommendation` service calls the `purchase-history` service, also via HTTP. You’ll use curl as your client, either outside of the cluster or in the cluster from the `sleep` pod. Pod anti-affinity rules are specified in the `sleep`, `web-api`, `recommendation` and `purchase-history` deployment descriptors to avoid them deployed on the same node whenever possible.

![Application without Istio](https://istio-ambient-mesh.s3.amazonaws.com/images/steps/deploy-fake-services/application-without-istio.png)

Deploy the sample application:

`kubectl apply -f data/steps/deploy-fake-services`

Note that you can use the `Files` tab to see the content of the different files we use in this workshop.

Run the following command to check the Pods deployed in the `default` namespace:

`kubectl get pods`

You’ll notice the following pods are deployed in the `default` namespace:

`NAME                                  READY   STATUS    RESTARTS   AGE notsleep-5564c6dd94-fnv6m             1/1     Running   0          88s purchase-history-v1-fc9446597-b9qt2   1/1     Running   0          88s recommendation-bb5cbdc9d-mxhhh        1/1     Running   0          88s sleep-84585fd6cc-k2fxb                1/1     Running   0          88s web-api-7c9755c6d-nhwn6               1/1     Running   0          88s`

Test the sample application using the following command:

`kubectl exec deploy/sleep -- curl http://web-api:8080/`

You should get something like this:

`{   "name": "web-api",   "uri": "/",   "type": "HTTP",   "ip_addresses": [     "10.101.2.5"   ],   "start_time": "2022-08-31T08:37:40.958489",   "end_time": "2022-08-31T08:37:40.961207",   "duration": "2.718015ms",   "body": "Hello From Web API",   "upstream_calls": [     {       "name": "recommendation",       "uri": "http://recommendation:8080",       "type": "HTTP",       "ip_addresses": [         "10.101.2.4"       ],       "start_time": "2022-08-31T08:37:40.959451",       "end_time": "2022-08-31T08:37:40.961007",       "duration": "1.555879ms",       "body": "Hello From Recommendations!",       "upstream_calls": [         {           "name": "purchase-history-v1",           "uri": "http://purchase-history:8080",           "type": "HTTP",           "ip_addresses": [             "10.101.1.4"           ],           "start_time": "2022-08-31T08:37:40.960476",           "end_time": "2022-08-31T08:37:40.960658",           "duration": "182.631µs",           "body": "Hello From Purchase History (v1)!",           "code": 200         }       ],       "code": 200     }   ],   "code": 200 }`


Expose service

Inbound traffic for the mesh is controlled with Istio ingress gateways, which can be configured with the Istio `Gateway` and `VirtualService` resources.

Create these resources to expose the `web-api` service through the Istio ingress gateway:

`kubectl apply -f data/steps/expose-web-api-service`

Get the external IP address of the Kubernetes Load Balancer service corresponding to the Istio ingress gateway:

`export GATEWAY_IP=$(kubectl -n istio-system get svc istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].*}')`

You should now be able to access the `web-api` service via Istio ingress gateway using the host header specified in the gateway resource.

`curl -H "Host: istioexplained.io" "http://${GATEWAY_IP}/"`


