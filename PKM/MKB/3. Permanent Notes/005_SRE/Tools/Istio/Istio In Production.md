

why should we use service mesh
1. Service discovery / load balancing
2. Secure service to service communication
3. Traffic control / shaping / shifting
4. Policy / Intention based access control
5. Traffic metric collection
6. API / Programmable interface

lab 1 Running envoy proxy

In this lab we will dig into one of the foundational pieces of Istio, the "data plane" or service proxy. These live with each service/application instance on the request path on both origination of a service call as well as usually on the destination side of the service call.

The service proxy that Istio uses is Envoy Proxy. Envoy is an incredibly powerful and well-suited proxy for this use case.

It's impossible to overstate how important Envoy is to Istio, which is why we start the labs with it.

## Set up supporting services

We use a simple httpbin service as well as sleep app to exercise the basic functionality of Envoy in this lab.

`kubectl apply -f labs/01/httpbin.yaml kubectl apply -f labs/01/sleep.yaml`

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: httpbin
---
apiVersion: v1
kind: Service
metadata:
  name: httpbin
  labels:
    app: httpbin
    service: httpbin
spec:
  ports:
  - name: http
    port: 8000
    targetPort: 80
  selector:
    app: httpbin
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpbin
spec:
  replicas: 1
  selector:
    matchLabels:
      app: httpbin
      version: v1
  template:
    metadata:
      labels:
        app: httpbin
        version: v1
    spec:
      serviceAccountName: httpbin
      containers:
      - image: docker.io/kennethreitz/httpbin
        imagePullPolicy: IfNotPresent
        name: httpbin
        ports:
        - containerPort: 80
```

```
# Copyright Istio Authors
#
#   Licensed under the Apache License, Version 2.0 (the "License");
#   you may not use this file except in compliance with the License.
#   You may obtain a copy of the License at
#
#       http://www.apache.org/licenses/LICENSE-2.0
#
#   Unless required by applicable law or agreed to in writing, software
#   distributed under the License is distributed on an "AS IS" BASIS,
#   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#   See the License for the specific language governing permissions and
#   limitations under the License.

##################################################################################################
# Sleep service
##################################################################################################
apiVersion: v1
kind: ServiceAccount
metadata:
  name: sleep
---
apiVersion: v1
kind: Service
metadata:
  name: sleep
  labels:
    app: sleep
    service: sleep
spec:
  ports:
  - port: 80
    name: http
  selector:
    app: sleep
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sleep
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sleep
  template:
    metadata:
      labels:
        app: sleep
    spec:
      serviceAccountName: sleep
      containers:
      - name: sleep
        image: governmentpaas/curl-ssl:terraform-14
        command: ["/bin/sleep", "3650d"]
        imagePullPolicy: IfNotPresent
        volumeMounts:
        - mountPath: /etc/sleep/tls
          name: secret-volume
      volumes:
      - name: secret-volume
        secret:
          secretName: sleep-secret
          optional: true
---
```

To verify we have things installed correctly, let's try running it:

`kubectl exec deploy/sleep -- curl -s httpbin:8000/headers`

Note, it may take a few moments for the sleep pod to come up, so you may need to retry the previous command if it fails

`{   "headers": {     "Accept": "*/*",     "Host": "httpbin:8000",     "User-Agent": "curl/7.69.1"   } }`

## Review Envoy proxy config

Envoy can be configured completely by loading a YAML/JSON file or in part with a dynamic API. The dynamic API is a big reason why microservice networking frameworks like Istio use Envoy, but we start by first understanding the configuration from a basic level.

We will use the file configuration format. Take a look at a simple configuration file:

`cat labs/01/envoy-conf.yaml`

`admin:   accessLogPath: /dev/stdout  address:     socketAddress:       address: 0.0.0.0      portValue: 15000 staticResources:   listeners:   - name: httpbin-listener     address:       socketAddress:         address: 0.0.0.0        portValue: 15001     filterChains:     - filters:       - name: envoy.filters.network.http_connection_manager        typedConfig:           '@type': type.googleapis.com/envoy.extensions.filters.network.http_connection_manager.v3.HttpConnectionManager          httpFilters:           - name: envoy.filters.http.router          routeConfig:             name: simple_httpbin_route            virtualHosts:             - domains:               - '*'               name: httpbin_host              routes:               - match:                   prefix: /                route:                   cluster: httpbin_service          statPrefix: httpbin  clusters:   - connectTimeout: 5s    loadAssignment:       clusterName: httpbin_service      endpoints:       - lbEndpoints:         - endpoint:             address:               socketAddress:                 address: httpbin                portValue: 8000     name: httpbin_service    respectDnsTtl: true     dnsLookupFamily: V4_ONLY    type: STRICT_DNS    upstreamConnectionOptions:       tcpKeepalive: {}`

In this configuration, we see three main sections:

-   [Admin] - Setting up the administration API for the proxy
-   [Listeners] - Declaration of ports to open on the proxy and listen for incoming connections
-   [Clusters] - Backend service to which we can route traffic

Let's now deploy this configuration to Envoy and deploy our Envoy Proxy:

`kubectl create cm envoy --from-file=envoy.yaml=./labs/01/envoy-conf.yaml -o yaml --dry-run=client | kubectl apply -f - kubectl apply -f labs/01/envoy-proxy.yaml`

Now let's try to call the Envoy Proxy and see that it correctly routes to the httpbin service:

`kubectl exec deploy/sleep -- curl -s http://envoy/headers`

Now our response from httpbin should look similar to this:

`{   "headers": {     "Accept": "*/*",     "Content-Length": "0",     "Host": "envoy",     "User-Agent": "curl/7.69.1",     "X-Envoy-Expected-Rq-Timeout-Ms": "15000"   } }`

We now see a response with some enriched response headers, X-Envoy-Expected-Rq-Timeout-Ms

## Change the call timeout

Now that we have Envoy on the request path of a service-to-service interaction, let's try changing the behavior of the call. With this default configuration, we saw an expected request timeout of 15s. Let's try to change the call timeout.

To change the call timeout, let's take a look at the routing configuration and add a parameter that specifies the timeout:

`routeConfig:   name: simple_httpbin_route  virtualHosts:   - domains:     - '*'     name: httpbin_host    routes:     - match:         prefix: /      route:         cluster: httpbin_service        timeout: 1s`

Here, we can see we set the timeout to 1s. Let's try to call the httpbin service again through the Envoy proxy. First, let's update the configuration:

`kubectl create cm envoy --from-file=envoy.yaml=./labs/01/envoy-conf-timeout.yaml -o yaml --dry-run=client | kubectl apply -f -`

We will also need to restart Envoy to pick up the new configuration:

`kubectl rollout restart deploy/envoy kubectl exec deploy/sleep -- curl -s http://envoy/headers`

We should see the headers now look like this:

`{   "headers": {     "Accept": "*/*",     "Content-Length": "0",     "Host": "envoy",     "User-Agent": "curl/7.69.1",     "X-Envoy-Expected-Rq-Timeout-Ms": "1000"   } }`

If we call our service, which takes longer than 1s, we should see a HTTP 504 / gateway timeout:

`kubectl exec deploy/sleep -- curl -vs http://envoy/delay/5`

`*   Trying 10.44.8.102:80... * Connected to envoy (10.44.8.102) port 80 (#0) > GET /delay/5 HTTP/1.1 > Host: envoy > User-Agent: curl/7.69.1 > Accept: */* > * Mark bundle as not supporting multiuse < HTTP/1.1 504 Gateway Timeout < content-length: 24 < content-type: text/plain < date: Thu, 11 Feb 2021 22:28:33 GMT < server: envoy < * Connection #0 to host envoy left intact upstream request timeout`

Although this is pretty simple so far, we can see how Envoy can become very valuable for a service-to-service request path. Enriching the networking with timeouts, retries, circuit breaking, etc. allow the service to focus on business logic and differentiating features vs. boring cross-cutting networking features.

## Admin stats

Now that we have a basic understanding of how to configure Envoy, let's take a look at another very important feature of Envoy proxy: stats and telemetry signals. One of the main reasons Envoy was even built was to give more visibility into what's happening on the network at the L7/request level. Let's take a look at how to get some of those stats.

Envoy exposes a /stats endpoint we can inspect:

`kubectl exec deploy/sleep -- curl -s http://envoy:15000/stats`

Wow, that's a lot of good info! Let's trim it down. Maybe we just want to see retry when the proxy calls the httpbin service:

`kubectl exec deploy/sleep -- curl -s http://envoy:15000/stats | grep retry`

`cluster.httpbin_service.circuit_breakers.default.rq_retry_open: 0 cluster.httpbin_service.circuit_breakers.high.rq_retry_open: 0 cluster.httpbin_service.retry_or_shadow_abandoned: 0 cluster.httpbin_service.upstream_rq_retry: 0 cluster.httpbin_service.upstream_rq_retry_backoff_exponential: 0 cluster.httpbin_service.upstream_rq_retry_backoff_ratelimited: 0 cluster.httpbin_service.upstream_rq_retry_limit_exceeded: 0 cluster.httpbin_service.upstream_rq_retry_overflow: 0 cluster.httpbin_service.upstream_rq_retry_success: 0 vhost.httpbin_host.vcluster.other.upstream_rq_retry: 0 vhost.httpbin_host.vcluster.other.upstream_rq_retry_limit_exceeded: 0 vhost.httpbin_host.vcluster.other.upstream_rq_retry_overflow: 0 vhost.httpbin_host.vcluster.other.upstream_rq_retry_success: 0`

Nice! Things have been running smoothly so far. No request retries. Let's change that.

## Retrying failed requests

Calling services over the network can get scary. Services don't always respond or may fail for some network reasons. We can have Envoy automatically retry when a request fails. Now, this isn't appropriate for every request, but Envoy can be tuned to be smarter about when to retry.

Let's see how to configure to retry on HTTP 5xx requests:

`cat labs/01/envoy-conf-retry.yaml`

`routeConfig:   name: simple_httpbin_route  virtualHosts:   - domains:     - '*'     name: httpbin_host    routes:     - match:         prefix: /      route:         cluster: httpbin_service        timeout: 1s        retryPolicy:           retryOn: 5xx          numRetries: 3`

Note the configuration for retries on 5xx.

Let's apply this new configuration:

`kubectl create cm envoy --from-file=envoy.yaml=./labs/01/envoy-conf-retry.yaml -o yaml --dry-run=client | kubectl apply -f - kubectl rollout restart deploy/envoy`

Now let's try to call an httpbin service endpoint that deliberately returns an error code:

`kubectl exec deploy/sleep -- curl -vs http://envoy/status/500`

We see the call fails:

`*   Trying 10.44.8.102:80... * Connected to envoy (10.44.8.102) port 80 (#0) > GET /status/500 HTTP/1.1 > Host: envoy > User-Agent: curl/7.69.1 > Accept: */* > * Mark bundle as not supporting multiuse < HTTP/1.1 500 Internal Server Error < server: envoy < date: Thu, 11 Feb 2021 23:34:32 GMT < content-type: text/html; charset=utf-8 < access-control-allow-origin: * < access-control-allow-credentials: true < content-length: 0 < x-envoy-upstream-service-time: 130 < * Connection #0 to host envoy left intact`

So let's see what Envoy observed in terms of retries:

`kubectl exec deploy/sleep -- curl http://envoy:15000/stats | grep retry`

`cluster.httpbin_service.circuit_breakers.default.rq_retry_open: 0 cluster.httpbin_service.circuit_breakers.high.rq_retry_open: 0 cluster.httpbin_service.retry.upstream_rq_500: 3 cluster.httpbin_service.retry.upstream_rq_5xx: 3 cluster.httpbin_service.retry.upstream_rq_completed: 3 cluster.httpbin_service.retry_or_shadow_abandoned: 0 cluster.httpbin_service.upstream_rq_retry: 3 cluster.httpbin_service.upstream_rq_retry_backoff_exponential: 3 cluster.httpbin_service.upstream_rq_retry_backoff_ratelimited: 0 cluster.httpbin_service.upstream_rq_retry_limit_exceeded: 1 cluster.httpbin_service.upstream_rq_retry_overflow: 0 cluster.httpbin_service.upstream_rq_retry_success: 0 vhost.httpbin_host.vcluster.other.upstream_rq_retry: 0 vhost.httpbin_host.vcluster.other.upstream_rq_retry_limit_exceeded: 0 vhost.httpbin_host.vcluster.other.upstream_rq_retry_overflow: 0 vhost.httpbin_host.vcluster.other.upstream_rq_retry_success: 0`

We see that indeed the call to httpbin did get retried 3 times.

## Recap

So far we have taken a basic approach to understanding what the Envoy proxy is and how to configure it. We have also seen how it can alter the behavior of a network call and give us valuable information about how the network is behaving at the request/message level.

## Next lab

In the next lab, we will dig into Istio's control plane a bit more. We'll also see how we can leverage all of these Envoy capabilities (resilience features, routing, observability, security, etc) to implement a secure, observable microservices architecture.

Lab 2 Installing istio

In the previous lab we saw how Envoy works. We also saw that Envoy needs a control plane to configure it in a dynamic environment like a cloud platform built on containers or Kubernetes.

Istio provides that control plane to drive the behavior of the network. Istio provides mechanisms for getting the Envoy proxy (also known as Istio service proxy, sidecar proxy, or data plane) integrated with workloads deployed to a system and for them to automatically connect to the control plane securely. Users can then use the control plane's API to drive the behavior of the network. Let's start installing and configuring Istio in this lab.

## Prerequisites

This lab will start by deploying some services in Kubernetes. The scenario we are replicating is one where Istio is being added to a set of workloads and that existing services are deployed into the cluster. In this lab (Lab 02) we will focus on getting Istio installed and in a later lab show how to iteratively roll out the mesh functionality to the workloads.

## Deploy the sample application

Let's set up the sample-apps:

`kubectl create ns istioinaction`

Now let's create some services:

`kubectl apply -n istioinaction -f sample-apps/web-api.yaml kubectl apply -n istioinaction -f sample-apps/recommendation.yaml kubectl apply -n istioinaction -f sample-apps/purchase-history-v1.yaml kubectl apply -n istioinaction -f sample-apps/sleep.yaml`

After running these commands, we should check the pods running in the istioinaction namespace:

`kubectl get po -n istioinaction`

`NAME                                   READY   STATUS    RESTARTS   AGE purchase-history-v1-6c8cb7f8f8-wn4dr   1/1     Running   0          22s recommendation-c9f7cc86f-nfvmk         1/1     Running   0          22s sleep-8f795f47d-5jfbn                  1/1     Running   0          14s web-api-6d544cff77-drrbm               1/1     Running   0          22s`

You now have some existing workloads in your cluster. Let's proceed to install the Istio control plane.

## Istio CLI installation

-   Download Istio

`curl -L https://istio.io/downloadIstio | ISTIO_VERSION=${ISTIO_VERSION} sh -`

-   Add the istioctl client to the PATH:

`export PATH=$PWD/istio-${ISTIO_VERSION}/bin:$PATH`

Check istioctl version:

`istioctl version`

We don't have the Istio control plane installed and running yet. Let's go ahead and do that. There are three ways to install Istio:

-   istioctl CLI tool
-   Istio Operator
-   Helm

We will use the istioctl approach to install Istio following some best practices to set you up for future success.

Helm 3 is another common approach to installing and upgrading Istio. We will have a lab demonstrating this in the next part of this workshop series.

## Installing Istio

Before we install Istio we will create a namespace where we will deploy Istio and create a Kubernetes service to represent the Istio control plane. Creating this service is needed to workaround a long-standing issue with Istio revisions until the Istio "tags" functionality makes it into the project.

Start with creating the namespace:

`kubectl create ns istio-system`

Next let's create the control plane service istiod:

`kubectl apply -f labs/02/istiod-service.yaml`

```
apiVersion: v1
kind: Service
metadata:
  labels:
    app: istiod
    istio: pilot
    release: istio
  name: istiod
  namespace: istio-system
spec:
  type: ClusterIP  
  ports:
  - name: grpc-xds
    port: 15010
  - name: https-dns
    port: 15012
  - name: https-webhook
    port: 443
    targetPort: 15017
  - name: http-monitoring
    port: 15014
  selector:
    app: istiod
```

Lastly, we will install the Istio control plane using the profile.

Our installation is "minimal" here as we will only be installing the istiod part of the control plane.

`istioctl install -y -n istio-system -f labs/02/control-plane.yaml --revision ${ISTIO_REVISION}`

Istio control plane yaml

```
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  name: control-plane
spec:
  profile: minimal
  meshConfig:              
    defaultConfig:       
      proxyMetadata:      
        ISTIO_META_DNS_CAPTURE: "true"
    enablePrometheusMerge: true  
```

You should see output similar to this:

`✔ Istio core installed ✔ Istiod installed ✔ Installation complete`

We now have Istio installed! This istiod component includes various functionality like:

-   xDS server for Envoy config
-   Certificate Authority for signing workload certs
-   Service discovery
-   Sidecar injection webhook

If we check the istio-system workspace, we should see the control plane running:

`kubectl get pod -n istio-system`

`NAME                            READY   STATUS    RESTARTS   AGE istiod-1-16-78b88c997d-rpnck   1/1     Running   0          2m1s`

From here, we can query the Istio control plane's debug endpoints to see what services we have running and what Istio has discovered.

`kubectl exec -n istio-system deploy/istiod-${ISTIO_REVISION} -- pilot-discovery request GET /debug/registryz | jq`

```
[
  {
    "Attributes": {
      "ServiceRegistry": "Kubernetes",
      "Name": "dashboard-metrics-scraper",
      "Namespace": "kubernetes-dashboard",
      "Labels": {
        "k8s-app": "dashboard-metrics-scraper"
      },
      "ExportTo": null,
      "LabelSelectors": {
        "k8s-app": "dashboard-metrics-scraper"
      },
      "ClusterExternalAddresses": {
        "Addresses": null
      },
      "ClusterExternalPorts": null
    },
    "ports": [
      {
        "port": 8000,
        "protocol": "UnsupportedProtocol"
      }
    ],
    "creationTime": "2022-12-14T18:19:12Z",
    "hostname": "dashboard-metrics-scraper.kubernetes-dashboard.svc.cluster.local",
    "clusterVIPs": {
      "Addresses": {
        "Kubernetes": [
          "10.43.161.66"
        ]
      }
    },
    "defaultAddress": "10.43.161.66",
    "Resolution": 0,
    "MeshExternal": false,
    "ResourceVersion": "242"
  },
  {
    "Attributes": {
      "ServiceRegistry": "Kubernetes",
      "Name": "envoy",
      "Namespace": "default",
      "Labels": {
        "app": "envoy",
        "service": "envoy"
      },
      "ExportTo": null,
      "LabelSelectors": {
        "app": "envoy"
      },
      "ClusterExternalAddresses": {
        "Addresses": null
      },
      "ClusterExternalPorts": null
    },
    "ports": [
      {
        "name": "http",
        "port": 80,
        "protocol": "HTTP"
      },
      {
        "name": "admin",
        "port": 15000,
        "protocol": "UnsupportedProtocol"
      }
    ],
    "creationTime": "2022-12-14T18:25:35Z",
    "hostname": "envoy.default.svc.cluster.local",
    "clusterVIPs": {
      "Addresses": {
        "Kubernetes": [
          "10.43.77.128"
        ]
      }
    },
    "defaultAddress": "10.43.77.128",
    "Resolution": 0,
    "MeshExternal": false,
    "ResourceVersion": "867"
  },
  {
    "Attributes": {
      "ServiceRegistry": "Kubernetes",
      "Name": "httpbin",
      "Namespace": "default",
      "Labels": {
        "app": "httpbin",
        "service": "httpbin"
      },
      "ExportTo": null,
      "LabelSelectors": {
        "app": "httpbin"
      },
      "ClusterExternalAddresses": {
        "Addresses": null
      },
      "ClusterExternalPorts": null
    },
    "ports": [
      {
        "name": "http",
        "port": 8000,
        "protocol": "HTTP"
      }
    ],
    "creationTime": "2022-12-14T18:23:08Z",
    "hostname": "httpbin.default.svc.cluster.local",
    "clusterVIPs": {
      "Addresses": {
        "Kubernetes": [
          "10.43.233.7"
        ]
      }
    },
    "defaultAddress": "10.43.233.7",
    "Resolution": 0,
    "MeshExternal": false,
    "ResourceVersion": "782"
  },
  {
    "Attributes": {
      "ServiceRegistry": "Kubernetes",
      "Name": "istiod-1-16",
      "Namespace": "istio-system",
      "Labels": {
        "app": "istiod",
        "install.operator.istio.io/owning-resource": "control-plane",
        "install.operator.istio.io/owning-resource-namespace": "istio-system",
        "istio": "pilot",
        "istio.io/rev": "1-16",
        "operator.istio.io/component": "Pilot",
        "operator.istio.io/managed": "Reconcile",
        "operator.istio.io/version": "1.16.0",
        "release": "istio"
      },
      "ExportTo": null,
      "LabelSelectors": {
        "app": "istiod",
        "istio.io/rev": "1-16"
      },
      "ClusterExternalAddresses": {
        "Addresses": null
      },
      "ClusterExternalPorts": null
    },
    "ports": [
      {
        "name": "grpc-xds",
        "port": 15010,
        "protocol": "GRPC"
      },
      {
        "name": "https-dns",
        "port": 15012,
        "protocol": "HTTPS"
      },
      {
        "name": "https-webhook",
        "port": 443,
        "protocol": "HTTPS"
      },
      {
        "name": "http-monitoring",
        "port": 15014,
        "protocol": "HTTP"
      }
    ],
    "creationTime": "2022-12-14T18:39:27Z",
    "hostname": "istiod-1-16.istio-system.svc.cluster.local",
    "clusterVIPs": {
      "Addresses": {
        "Kubernetes": [
          "10.43.62.182"
        ]
      }
    },
    "defaultAddress": "10.43.62.182",
    "Resolution": 0,
    "MeshExternal": false,
    "ResourceVersion": "1352"
  },
  {
    "Attributes": {
      "ServiceRegistry": "Kubernetes",
      "Name": "istiod",
      "Namespace": "istio-system",
      "Labels": {
        "app": "istiod",
        "istio": "pilot",
        "release": "istio"
      },
      "ExportTo": null,
      "LabelSelectors": {
        "app": "istiod"
      },
      "ClusterExternalAddresses": {
        "Addresses": null
      },
      "ClusterExternalPorts": null
    },
    "ports": [
      {
        "name": "grpc-xds",
        "port": 15010,
        "protocol": "GRPC"
      },
      {
        "name": "https-dns",
        "port": 15012,
        "protocol": "HTTPS"
      },
      {
        "name": "https-webhook",
        "port": 443,
        "protocol": "HTTPS"
      },
      {
        "name": "http-monitoring",
        "port": 15014,
        "protocol": "HTTP"
      }
    ],
    "creationTime": "2022-12-14T18:38:23Z",
    "hostname": "istiod.istio-system.svc.cluster.local",
    "clusterVIPs": {
      "Addresses": {
        "Kubernetes": [
          "10.43.79.149"
        ]
      }
    },
    "defaultAddress": "10.43.79.149",
    "Resolution": 0,
    "MeshExternal": false,
    "ResourceVersion": "1238"
  },
  {
    "Attributes": {
      "ServiceRegistry": "Kubernetes",
      "Name": "kube-dns",
      "Namespace": "kube-system",
      "Labels": {
        "k8s-app": "kube-dns",
        "kubernetes.io/cluster-service": "true",
        "kubernetes.io/name": "CoreDNS",
        "objectset.rio.cattle.io/hash": "bce283298811743a0386ab510f2f67ef74240c57"
      },
      "ExportTo": null,
      "LabelSelectors": {
        "k8s-app": "kube-dns"
      },
      "ClusterExternalAddresses": {
        "Addresses": null
      },
      "ClusterExternalPorts": null
    },
    "ports": [
      {
        "name": "dns",
        "port": 53,
        "protocol": "UDP"
      },
      {
        "name": "dns-tcp",
        "port": 53,
        "protocol": "TCP"
      },
      {
        "name": "metrics",
        "port": 9153,
        "protocol": "UnsupportedProtocol"
      }
    ],
    "creationTime": "2022-12-14T18:19:13Z",
    "hostname": "kube-dns.kube-system.svc.cluster.local",
    "clusterVIPs": {
      "Addresses": {
        "Kubernetes": [
          "10.43.0.10"
        ]
      }
    },
    "defaultAddress": "10.43.0.10",
    "Resolution": 0,
    "MeshExternal": false,
    "ResourceVersion": "268"
  },
  {
    "Attributes": {
      "ServiceRegistry": "Kubernetes",
      "Name": "kubernetes-dashboard",
      "Namespace": "kubernetes-dashboard",
      "Labels": {
        "k8s-app": "kubernetes-dashboard"
      },
      "ExportTo": null,
      "LabelSelectors": {
        "k8s-app": "kubernetes-dashboard"
      },
      "ClusterExternalAddresses": {
        "Addresses": null
      },
      "ClusterExternalPorts": null
    },
    "ports": [
      {
        "port": 443,
        "protocol": "UnsupportedProtocol"
      }
    ],
    "creationTime": "2022-12-14T18:19:12Z",
    "hostname": "kubernetes-dashboard.kubernetes-dashboard.svc.cluster.local",
    "clusterVIPs": {
      "Addresses": {
        "Kubernetes": [
          "10.43.94.107"
        ]
      }
    },
    "defaultAddress": "10.43.94.107",
    "Resolution": 0,
    "MeshExternal": false,
    "ResourceVersion": "230"
  },
  {
    "Attributes": {
      "ServiceRegistry": "Kubernetes",
      "Name": "kubernetes",
      "Namespace": "default",
      "Labels": {
        "component": "apiserver",
        "provider": "kubernetes"
      },
      "ExportTo": null,
      "LabelSelectors": null,
      "ClusterExternalAddresses": {
        "Addresses": null
      },
      "ClusterExternalPorts": null
    },
    "ports": [
      {
        "name": "https",
        "port": 443,
        "protocol": "HTTPS"
      }
    ],
    "creationTime": "2022-12-14T18:19:09Z",
    "hostname": "kubernetes.default.svc.cluster.local",
    "clusterVIPs": {
      "Addresses": {
        "Kubernetes": [
          "10.43.0.1"
        ]
      }
    },
    "defaultAddress": "10.43.0.1",
    "Resolution": 0,
    "MeshExternal": false,
    "ResourceVersion": "197"
  },
  {
    "Attributes": {
      "ServiceRegistry": "Kubernetes",
      "Name": "metrics-server",
      "Namespace": "kube-system",
      "Labels": {
        "kubernetes.io/cluster-service": "true",
        "kubernetes.io/name": "Metrics-server",
        "objectset.rio.cattle.io/hash": "a5d3bc601c871e123fa32b27f549b6ea770bcf4a"
      },
      "ExportTo": null,
      "LabelSelectors": {
        "k8s-app": "metrics-server"
      },
      "ClusterExternalAddresses": {
        "Addresses": null
      },
      "ClusterExternalPorts": null
    },
    "ports": [
      {
        "name": "https",
        "port": 443,
        "protocol": "HTTPS"
      }
    ],
    "creationTime": "2022-12-14T18:19:14Z",
    "hostname": "metrics-server.kube-system.svc.cluster.local",
    "clusterVIPs": {
      "Addresses": {
        "Kubernetes": [
          "10.43.54.105"
        ]
      }
    },
    "defaultAddress": "10.43.54.105",
    "Resolution": 0,
    "MeshExternal": false,
    "ResourceVersion": "319"
  },
  {
    "Attributes": {
      "ServiceRegistry": "Kubernetes",
      "Name": "purchase-history",
      "Namespace": "istioinaction",
      "Labels": null,
      "ExportTo": null,
      "LabelSelectors": {
        "app": "purchase-history"
      },
      "ClusterExternalAddresses": {
        "Addresses": null
      },
      "ClusterExternalPorts": null
    },
    "ports": [
      {
        "name": "http",
        "port": 8080,
        "protocol": "HTTP"
      }
    ],
    "creationTime": "2022-12-14T18:37:12Z",
    "hostname": "purchase-history.istioinaction.svc.cluster.local",
    "clusterVIPs": {
      "Addresses": {
        "Kubernetes": [
          "10.43.25.92"
        ]
      }
    },
    "defaultAddress": "10.43.25.92",
    "Resolution": 0,
    "MeshExternal": false,
    "ResourceVersion": "1154"
  },
  {
    "Attributes": {
      "ServiceRegistry": "Kubernetes",
      "Name": "recommendation",
      "Namespace": "istioinaction",
      "Labels": null,
      "ExportTo": null,
      "LabelSelectors": {
        "app": "recommendation"
      },
      "ClusterExternalAddresses": {
        "Addresses": null
      },
      "ClusterExternalPorts": null
    },
    "ports": [
      {
        "name": "http",
        "port": 8080,
        "protocol": "HTTP"
      }
    ],
    "creationTime": "2022-12-14T18:37:11Z",
    "hostname": "recommendation.istioinaction.svc.cluster.local",
    "clusterVIPs": {
      "Addresses": {
        "Kubernetes": [
          "10.43.131.115"
        ]
      }
    },
    "defaultAddress": "10.43.131.115",
    "Resolution": 0,
    "MeshExternal": false,
    "ResourceVersion": "1138"
  },
  {
    "Attributes": {
      "ServiceRegistry": "Kubernetes",
      "Name": "sleep",
      "Namespace": "default",
      "Labels": {
        "app": "sleep",
        "service": "sleep"
      },
      "ExportTo": null,
      "LabelSelectors": {
        "app": "sleep"
      },
      "ClusterExternalAddresses": {
        "Addresses": null
      },
      "ClusterExternalPorts": null
    },
    "ports": [
      {
        "name": "http",
        "port": 80,
        "protocol": "HTTP"
      }
    ],
    "creationTime": "2022-12-14T18:23:08Z",
    "hostname": "sleep.default.svc.cluster.local",
    "clusterVIPs": {
      "Addresses": {
        "Kubernetes": [
          "10.43.189.179"
        ]
      }
    },
    "defaultAddress": "10.43.189.179",
    "Resolution": 0,
    "MeshExternal": false,
    "ResourceVersion": "800"
  },
  {
    "Attributes": {
      "ServiceRegistry": "Kubernetes",
      "Name": "sleep",
      "Namespace": "istioinaction",
      "Labels": {
        "app": "sleep"
      },
      "ExportTo": null,
      "LabelSelectors": {
        "app": "sleep"
      },
      "ClusterExternalAddresses": {
        "Addresses": null
      },
      "ClusterExternalPorts": null
    },
    "ports": [
      {
        "name": "http",
        "port": 80,
        "protocol": "HTTP"
      }
    ],
    "creationTime": "2022-12-14T18:37:12Z",
    "hostname": "sleep.istioinaction.svc.cluster.local",
    "clusterVIPs": {
      "Addresses": {
        "Kubernetes": [
          "10.43.16.152"
        ]
      }
    },
    "defaultAddress": "10.43.16.152",
    "Resolution": 0,
    "MeshExternal": false,
    "ResourceVersion": "1170"
  },
  {
    "Attributes": {
      "ServiceRegistry": "Kubernetes",
      "Name": "web-api",
      "Namespace": "istioinaction",
      "Labels": null,
      "ExportTo": null,
      "LabelSelectors": {
        "app": "web-api"
      },
      "ClusterExternalAddresses": {
        "Addresses": null
      },
      "ClusterExternalPorts": null
    },
    "ports": [
      {
        "name": "http",
        "port": 8080,
        "protocol": "HTTP"
      }
    ],
    "creationTime": "2022-12-14T18:37:10Z",
    "hostname": "web-api.istioinaction.svc.cluster.local",
    "clusterVIPs": {
      "Addresses": {
        "Kubernetes": [
          "10.43.182.192"
        ]
      }
    },
    "defaultAddress": "10.43.182.192",
    "Resolution": 0,
    "MeshExternal": false,
    "ResourceVersion": "1117"
  }
]
```


The output of this command can be quite verbose as it lists all of the services in the Istio registry. Workloads are included in the Istio registry even if they are not officially part of the mesh (ie, have a sidecar deployed next to it). We leave it to the reader to grep for some of the previously deployed services (web-api, recommendation and purchase-history services). We will cover more of the debug endpoints in Lab 08

## Install sidecar for demo app

In this section we'll install a sidecar onto the httpbin service from the previous lab and explore it.

Run the following command to add the Istio sidecar to the httpbin service in the default namespasce:

`k label namespace default istio.io/rev=${ISTIO_REVISION} k apply -f labs/01/httpbin.yaml k rollout restart deployment httpbin`

In the above command we configure istioctl to use the configmaps for our revision. We can run multiple versions of Istio concurrently and can specify exactly which revision gets applied in the tooling.

## Recap

At this point we have installed the Istio control plane following a slightly different method than the official docs, but one that sets us up for success for operating Istio.

## Next lab

In the next lab we will leverage Istio's telemetry collection and scrape it into Prometheus, Grafana, and Kiali. Istio ships with some sample addons to install those components in a POC environment, but we'll look at a more realistic environment.

