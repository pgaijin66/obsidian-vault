
Installing istio

1. Download [[istioctl]] binary `curl -L https://istio.io/downloadIstio | ISTIO_VERSION=${ISTIO_VERSION} sh -`
2. Add istioctl to PATH `export PATH=$PWD/istio-${ISTIO_VERSION}/bin:$PATH`
3. Check istioctl version `istioctl version`
4. Check if k8s env meetds istio platform requirement `istioctl x precheck`
5. List available profiles `istioctl profile list`
6. choose demo `istioctl install --set profile=demo -y`
7. Check `kubectl get all,cm,secrets,envoyfilters -n istio-system`
8. Verify installation `istioctl verify-install`

find all available crds
`kubectl get crds -n istio-system`



Installing istio telemetry add-ons
1. Telemetry `kubectl apply -f istio-${ISTIO_VERSION}/samples/addons`
2. wait till all pods in istio-system are running `kubectl get pods -n istio-system`
3. Enable promethues dashboard `istioctl dashboard prometheus --browser=false --address 0.0.0.0`
4. Enable grafana `istioctl dashboard grafana --browser=false --address 0.0.0.0`
5. Enable jeager `istioctl dashboard jaeger --browser=false --address 0.0.0.0
6. Enable kiali dashboard `istioctl dashboard kiali --browser=false --address 0.0.0.0`
![](https://play.instruqt.com/assets/tracks/yhihb6wxbfbq/72a1e9a1c27f82ee04f03750432ee87e/assets/diagram-istio-ingress.png)
Istio ingress gateway
1. Setup gateway and virtual service
	1. Gateway for headers manupulation
	2. virtrual sertver for routing to k8s service
3. query the gateway configuration using `istioctl proxy-config routes deploy/istio-ingressgateway.istio-system`
4. see individual routes `istioctl proxy-config routes deploy/istio-ingressgateway.istio-system --name http.8080 -o json`

Secure outbound traffic
![Secure Istio Ingress Gateway](https://play.instruqt.com/assets/tracks/yhihb6wxbfbq/5ad5c2863ac7805916d059491b99731b/assets/diagram-secure-istio-ingress.png)
1. To secure inbound traffic with HTTPS, you need a certificate with the appropriate SAN and you will need to configure the Istio ingress-gateway to use it.
2.  Create a TLS secret for `istioinaction.io` in the `istio-system` namespace:
    
    `kubectl create -n istio-system secret tls istioinaction-cert --key labs/02/certs/istioinaction.io.key --cert labs/02/certs/istioinaction.io.crt`
    
2.  Update the Istio ingress-gateway to use this cert:
    
    `cat labs/02/web-api-gw-https.yaml`


```yaml
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: web-api-gateway
  namespace: istioinaction
spec:
  selector:
    istio: ingressgateway 
  servers:
  - port:
      number: 443
      name: https
      protocol: HTTPS
    hosts:
    - "istioinaction.io"    
    tls:
      mode: SIMPLE
      credentialName: istioinaction-cert
```

    
    Note, we are pointing to the `istioinaction-cert` and that **the cert must be in the same namespace as the ingress gateway deployment**. Even though the `Gateway` resource is in the `istioinaction` namespace, _the cert must be where the gateway is actually deployed_.
    
3.  Apply the `web-api-gw-https.yaml` in the `istioinaction` namespace. Since this gateway resource is also called `web-api-gateway`, it will replace our prior `web-api-gateway` configuration for port `80`.
    
    `kubectl -n istioinaction apply -f labs/02/web-api-gw-https.yaml`
    
4.  Call the `web-api` service through the Istio ingress-gateway on the secure `443` port:
    
    `curl --cacert ./labs/02/certs/ca/root-ca.crt -H "Host: istioinaction.io" https://istioinaction.io:$SECURE_INGRESS_PORT --resolve istioinaction.io:$SECURE_INGRESS_PORT:$GATEWAY_IP`
    
5.  If you call it on the `80` port with `http`, it will not work as you no longer have the gateway resource configured to be exposed on port `80`.
    
    `curl -H "Host: istioinaction.io" http://$GATEWAY_IP:$INGRESS_PORT`


Lab 3 istio 

In this lab, you will incrementally add services to the mesh. The mesh is actually integrated with the services themselves which makes it mostly transparent to the service implementation.

## Sidecar injection

Adding services to the mesh requires that the client-side proxies be associated with the service components and registered with the control plane. With Istio, you have two methods to inject the Envoy Proxy sidecar into the microservice Kubernetes pods:

-   Automatic sidecar injection
-   Manual sidecar injection.

1.  To enable the automatic sidecar injection, use the command below to add the `istio-injection` label to the `istioinaction` namespace:
    
    `kubectl label namespace istioinaction istio-injection=enabled`
    
2.  Validate the `istioinaction` namespace is annotated with the `istio-injection` label:
    
    `kubectl get namespace -L istio-injection`
    

Now that you have an `istioinaction` namespace with automatic sidecar injection enabled, you are ready to start adding services in your `istioinaction` namespace to the mesh. Since you added the `istio-injection` label to the `istioinaction` namespace, the Istio mutating admission controller automatically injects the Envoy Proxy sidecar during the initial deployment or restart of the pod.

## Review Service requirements

Before you add Kubernetes services to the mesh, you need to be aware of the [application requirements](https://istio.io/latest/docs/ops/deployment/requirements/) to ensure that your Kubernetes services meet the minimum requirements.

Service descriptors:

-   each service port name must start with the protocol name, for example `name: http`

Deployment descriptors:

-   The pods must be associated with a Kubernetes service.
-   The pods must not run as a user with UID 1337
-   App and version labels are added to provide contextual information for metrics and tracing

Check the above requirements for each of the Kubernetes services and make adjustments as necessary. If you don't have `NET_ADMIN` security rights, you would need to explore the [Istio CNI plugin](https://istio.io/latest/docs/setup/additional-setup/cni/) to remove the `NET_ADMIN` requirement for deploying services.

Using the `web-api` service as an example, you can review its service and deployment descriptor:

`cat sample-apps/web-api.yaml`


```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: web-api
---
# Service descriptor to expose web frontend
apiVersion: v1
kind: Service
metadata:
  name: web-api
spec:
  selector:
    app: web-api
  ports:
  - name: http
    protocol: TCP
    port: 8080
    targetPort: 8081
---
# Deployment descriptor for web-api   
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-api
  labels:
    app: web-api
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web-api
      version: v1
  template:
    metadata:
      labels:
        app: web-api
        version: v1
      annotations:
    spec:
      serviceAccountName: web-api    
      containers:
      - name: web-api
        image: nicholasjackson/fake-service:v0.7.8
        ports:
        - containerPort: 8081
        env:
        - name: "LISTEN_ADDR"
          value: "0.0.0.0:8081"
        - name: "UPSTREAM_URIS"
          value: "http://recommendation:8080"
        - name: "NAME"
          value: "web-api"
        - name: "MESSAGE"
```

From the service descriptor, the `name: http` declares the `http` protocol for the service port `8080`:

  `- name: http    protocol: TCP    port: 8080     targetPort: 8081`

From the deployment descriptor, the `app: web-api` label matches the `web-api` service's selector of `app: web-api` so this deployment and its pod are associated with the `web-api` service. Further, the `app: web-api` label and `version: v1` labels provide contextual information for metrics and tracing. The `containerPort: 8081` declares the listening port for the container, which matches the `targetPort: 8081` in the `web-api` service descriptor earlier.

  `template:     metadata:       labels:         app: web-api         version: v1      annotations:     spec:       serviceAccountName: web-api       containers:       - name: web-api         image: nicholasjackson/fake-service:v0.7.8         ports:         - containerPort: 8081`

Check the `purchase-history-v1`, `recommendation`, and `sleep` services to validate they all meet the above requirements.

## Adding services to the mesh

1.  You can add a sidecar to each of the services in the `istioinaction` namespace, starting with the `web-api` service:
    
    `kubectl rollout restart deployment web-api -n istioinaction`
    
2.  Validate the `web-api` pod is running with Istio's default sidecar proxy injected:
    
    `kubectl get pod -l app=web-api -n istioinaction`
    
    You should see `2/2` in the output. This indicates the sidecar proxy is running alongside the `web-api` application container in the `web-api` pod:
    
    `NAME                       READY   STATUS    RESTARTS   AGE web-api-7d5ccfd7b4-m7lkj   2/2     Running   0          9m4s`
    
3.  Validate the `web-api` pod log looks good:
    
    `kubectl logs deploy/web-api -c web-api -n istioinaction`
    
4.  Validate you can continue to call the `web-api` service securely:
    
    `curl --cacert ./labs/02/certs/ca/root-ca.crt -H "Host: istioinaction.io" https://istioinaction.io:$SECURE_INGRESS_PORT --resolve istioinaction.io:$SECURE_INGRESS_PORT:$GATEWAY_IP`
    

## Understand what happens

Use the command below to get the details of the `web-api` pod:

`kubectl get pod -l app=web-api -n istioinaction -o yaml`

From the output, the `web-api` pod contains 1 init container and 2 normal containers. The Istio mutating admission controller was responsible for injecting the `istio-init` container and the `istio-proxy` container.

## The `istio-init` container:

The `istio-init` container uses the `proxyv2` image. The entry point of the container is `pilot-agent`, which contains the `istio-iptables` command to set up port forwarding for Istio's sidecar proxy.

    `initContainers:    - args:       - istio-iptables       - -p       - "15001"       - -z       - "15006"       - -u       - "1337"       - -m       - REDIRECT       - -i       - '*'       - -x       - ""       - -b       - '*'       - -d       - 15090,15021,15020       image: docker.io/istio/proxyv2:latest       imagePullPolicy: Always       name: istio-init`

Interested in knowing more about the flags for `istio-iptables`? Run the following command:

`kubectl exec deploy/web-api -c istio-proxy -n istioinaction -- /usr/local/bin/pilot-agent istio-iptables --help`

The output explains the flags such as `-u`, `-m`, and `-i` which are used in the `istio-init` container's `args`. You will notice that all inbound ports are redirected to the Envoy Proxy container within the pod. You can also see a few ports such as `15021` which are excluded from redirection (you'll soon learn why this is the case). You may also notice the following `securityContext` for the `istio-init` container. This means that a service deployer must have the `NET_ADMIN` and `NET_RAW` security capabilities to run the `istio-init` container for the `web-api` service or other services in the Istio service mesh. If the service deployer can't have these security capabilities, you can use the [Istio CNI plugin](https://istio.io/latest/docs/setup/additional-setup/cni/) which removes the `NET_ADMIN` and `NET_RAW` requirement for users deploying pods into Istio service mesh.

## The `istio-proxy` container:

When you continue looking through the list of containers in the pod, you will see the `istio-proxy` container. The `istio-proxy` container also uses the `proxyv2` image. You'll notice the `istio-proxy` container has requested 0.01 CPU and 40 MB memory to start with as well as 2 CPU and 1 GB memory for limits. You will need to budget for these settings when managing the capacity for the cluster. These resources can be customized during the Istio installation thus may vary per your [installation profile](https://istio.io/latest/docs/setup/additional-setup/config-profiles/).

When you reviewed the `istio-init` container configuration earlier, you may have noticed that ports `15021`, `15090`, and `15020` are on the list of inbound ports to be excluded from redirection to Envoy. The reason is that port `15021` is for health check, and port `15090` is for the Envoy Proxy to emit its metrics to Prometheus, and port `15020` is for the merged Prometheus metrics colllected from the Istio agent, the Envoy Proxy, and the application container. Thus it is not necessary to redirect inbound traffic for these ports since they are being used by the `istio-proxy` container.

Also notice that the `istiod-ca-cert` and `istio-token` volumes are mounted on the pod for the purpose of implementing mutual TLS (mTLS), which will be covered in the [lab 04](https://play.instruqt.com/embed/soloio/tracks/get-started-istio/challenges/adding-services-to-mesh/04-secure-services-with-istio.md).

## Add more services to the Istio service mesh

1.  Next, you can add the `istio-proxy` sidecar to the other services in the `istioinaction` namespace
    
    `kubectl rollout restart deployment purchase-history-v1 -n istioinaction kubectl rollout restart deployment recommendation -n istioinaction kubectl rollout restart deployment sleep -n istioinaction`
    
2.  Validate that all the pods in the `istioinaction` namespace are running with Istio's default sidecar proxy injected:
    
    `kubectl get pods -n istioinaction`
    
3.  Validate that you can continue to call the `web-api` service securely:
    
    `curl --cacert ./labs/02/certs/ca/root-ca.crt -H "Host: istioinaction.io" https://istioinaction.io:$SECURE_INGRESS_PORT --resolve istioinaction.io:$SECURE_INGRESS_PORT:$GATEWAY_IP`
    

## What have you gained?

Congratulations on adding services in the `istioinaction` namespace to the Istio service mesh. One of the values of using a service mesh is that you will gain immediate insight into the behavior and interactions between your services. Istio delivers a set of dashboards as add-on components that give you access to important telemetry data, just by adding services to the mesh.

## Distributed tracing

You can view distributed tracing information using the Jaeger UI tab.

-   Navigate to the Jaeger UI tab. On the "Service" dropdown, select "istio-ingressgateway". Click on the "Find Traces" button at the bottom. You should see some traces, which show every request to the `web-api` service through the Istio's ingress gateway.
    
-   Click on one of the traces to view the details of the distributed traces for that request. You can click on each trace span to learn more about it. You may notice all trace spans have the same value for the `x-request-id` header. Why? This is how Jaeger knows these trace spans are part of the same request. In order for your services' distributed tracing to work properly in your Istio service mesh, the [B-3 trace headers](https://istio.io/latest/docs/tasks/observability/distributed-tracing/overview/#trace-context-propagation) including `x-request-id` have to be propagated between your services.


Lab 4 mtls

### Securing Communication Within Istio

In the previous lab, you explored adding services into a mesh. When you installed Istio using the demo profile, it is using a permissive security mode. The Istio permissive security setting is useful when you have services that are being moved into the service mesh incrementally, as it allows both plain text and mTLS traffic. In this lab, you will explore how Istio manages secure communication between services and how to require strict security between services in the sample application.

## Prerequisites

Verify you're in the correct folder for this lab: `/root/istio-workshops/istio-basics`. This lab builds on earlier work where you added your services to the mesh.

`cd /root/istio-workshops/istio-basics`

## Permissive mode

By default, Istio automatically upgrades the connection securely from the source service's sidecar proxy to the target service's sidecar proxy. This is why you saw the lock icon in the Kiali graph earlier in the connections between the Istio ingress gateway, the `web-api` service, the `history` service, and the `recommendation` service. While it is probably ok when onboarding your services to your Istio service mesh to have communications between source and target services allowed via plain text if mutual TLS communication fails, you wouldn't want this in a production environment. You need to have a proper security policy in place.

Check if you have a `peerauthentication` policy in any of your namespaces:

`kubectl get peerauthentication --all-namespaces`

You should see `No resources found` in the output, which means no peer authentication has been specified and the default `PERMISSIVE` mTLS mode is being used.

## Enable strict mTLS

1.  You can lock down access to the services in your Istio service mesh to securely require mTLS using a peer authentication policy. Execute this command to define a default policy for the `istio-system` namespace that updates all of the servers to accept only mTLS traffic:
    
    `kubectl apply -n istio-system -f - <<EOF apiVersion: "security.istio.io/v1beta1" kind: "PeerAuthentication" metadata:   name: "default" spec:   mtls:    mode: STRICT EOF`
    
2.  Verify your `peerauthentication` policy is installed:
    
    `kubectl get peerauthentication --all-namespaces`
    
    You should see the the default `peerauthentication` policy installed in the `istio-system` namespace with STRICT mTLS enabled:
    
    `NAMESPACE      NAME      MODE     AGE istio-system   default   STRICT   84s`
    
    Because the `istio-system` namespace is also the Istio mesh configuration root namespace in your environment, this `peerauthentication` policy is the default policy for all of your services in the mesh regardless of which namespaces your services run.
    
3.  You can send some traffic to `web-api` from a pod that is not part of the Istio service mesh. Deploy the `sleep` service and pod in the `default` namespace:
    
    `kubectl apply -n default -f sample-apps/sleep.yaml`
    
4.  Access the `web-api` service from the `sleep` pod in the `default` namespace:
    
    `kubectl exec deploy/sleep -n default -- curl http://web-api.istioinaction:8080/`
    
    The request will fail because the `web-api` service can only be accessed with mutual TLS connections. The `sleep` pod in the `default` namespace doesn't have the sidecar proxy, so it doesn't have the needed keys and certificates to communicate to the `web-api` service via mutual TLS.
    
5.  Run the same command from the `sleep` pod in the `istioinaction` namespace:
    
    `kubectl exec deploy/sleep -n istioinaction -- curl http://web-api.istioinaction:8080/`
    
    You should now see the request succeed.
    

## Visualize mTLS enforcement in Kiali

You can visualize the services in the mesh in Kiali.

1.  Generate some load to the data plane (by calling our `web-api` service) so that you can observe interactions among your services:
    
    `for i in {1..200};   do curl --cacert ./labs/02/certs/ca/root-ca.crt -H "Host: istioinaction.io" https://istioinaction.io:$SECURE_INGRESS_PORT  --resolve istioinaction.io:$SECURE_INGRESS_PORT:$GATEWAY_IP;   sleep 3; done`
    
2.  Navigate to the Kiali tab and select the Graph tab.
    
    On the "Namespace" dropdown, select "istioinaction". On the "Display" drop down, select "Traffic Animation" and "Security".
    
3.  You should observe the service interaction graph with some traffic animation and security badges.
    
4.  Return to the **Terminal** tab and enter **ctrl+c** to end the load generation.
    

## Understand how mTLS works in Istio service mesh

1.  Inspect the key and/or certificates used by Istio for the `web-api` service in the `istioinaction` namespace:
    
    `istioctl proxy-config secret deploy/web-api -n istioinaction`
    
    From the output, you'll see the default secret and your Istio service mesh's root CA public certificate.
    
    The `default` secret containers the public certificate information for the `web-api` service. You can analyze the contents of the default secret using openssl.
    
2.  Check the issuer of the public certificate:
    
    `istioctl proxy-config secret deploy/web-api -n istioinaction -o json | jq '[.dynamicActiveSecrets[] | select(.name == "default")][0].secret.tlsCertificate.certificateChain.inlineBytes' -r | base64 -d | openssl x509 -noout -text | grep 'Issuer'`
    
3.  Check if the public certificate in the default secret is valid:
    
    `istioctl proxy-config secret deploy/web-api -n istioinaction -o json | jq '[.dynamicActiveSecrets[] | select(.name == "default")][0].secret.tlsCertificate.certificateChain.inlineBytes' -r | base64 -d | openssl x509 -noout -text | grep 'Validity' -A 2`
    
    You should see the public certificate is valid and expires in 24 hours.
    
4.  Validate the identity of the client certificate is correct:
    
    `istioctl proxy-config secret deploy/web-api -n istioinaction -o json | jq '[.dynamicActiveSecrets[] | select(.name == "default")][0].secret.tlsCertificate.certificateChain.inlineBytes' -r | base64 -d | openssl x509 -noout -text | grep 'Subject Alternative Name' -A 1`
    
    You should see the identity of the `web-api` service. Note it is using the SPIFFE format, e.g. `spiffe://{my-trust-domain}/ns/{namespace}/sa/{service-account}`.
    

## Understand the SPIFFE format used by Istio

Where do the `cluster.local` and `web-api` values come from? Check the `istio` configmap in the `istio-system` namespace:

`kubectl get cm istio -n istio-system -o yaml | grep trustDomain -m 1`

You'll see `cluster.local` returned as the trustDomain value, per the installation of your Istio using the demo profile.

    `trustDomain: cluster.local`

If you review the `sample-apps/web-api.yaml` file, you will see the `web-api` service account in there.

`cat sample-apps/web-api.yaml | grep ServiceAccount -A 3`

`kind: ServiceAccount metadata:   name: web-api ---`

## How does the `web-api` service obtain the needed key and/or certificates?

Earlier, you reviewed the injected `istio-proxy` container for the `web-api` pod. Recall there are a few volumes mounted to the `istio-proxy` container.

      ``volumeMounts`      - mountPath: /var/run/secrets/istio         name: istiod-ca-cert       - mountPath: /var/lib/istio/data         name: istio-data       - mountPath: /etc/istio/proxy         name: istio-envoy       - mountPath: /var/run/secrets/tokens         name: istio-token       - mountPath: /etc/istio/pod         name: istio-podinfo       - mountPath: /var/run/secrets/kubernetes.io/serviceaccount         name: web-api-token-ztk5d         readOnly: true ...     - name: istio-token       projected:         defaultMode: 420         sources:         - serviceAccountToken:             audience: istio-ca             expirationSeconds: 43200             path: istio-token     - configMap:         defaultMode: 420         name: istio-ca-root-cert       name: istiod-ca-cert     - name: web-api-token-ztk5d       secret:         defaultMode: 420         secretName: web-api-token-ztk5d``

The `istio-ca-cert` mounted volume is from the `istio-ca-root-cert` configmap in the `istioinaction` namespace. During start up time, Istio agent (also called `pilot-agent`) creates the private key for the `web-api` service and then sends the certificate signing request (CSR) for the Istio CA (Istio control plane is the Istio CA in your installation) to sign the private key, using the `istio-token` and the `web-api` service's service account token `web-api-token-ztk5d` as credentials. The Istio agent sends the certificates received from the Istio CA along with the private key to the Envoy Proxy via the Envoy SDS API.

You noticed earlier that the certificate expires in 24 hours. What happens when the certificate expires? The Istio agent monitors the `web-api` certificate for expiration and repeats the CSR request process described above periodically to ensure each of your workload's certificate is still valid.

## How is mTLS strict enforced?

When mTLS strict is enabled, you will find the Envoy Proxy configuration for the `istio-proxy` container actually has fewer lines of configurations. This is because when mTLS strict is enabled, you would only allow mTLS traffic and therefore don't need or want filter chain configurations to allow plain text traffic to services in the mesh (hint: search for `"transport_protocol": "raw_buffer"` in your Envoy configuration when `PERMISSIVE` mode is applied). If you are curious to explore Envoy configuration for any of your pod, you can use the command below to view the configuration for the `istio-proxy` container of the `web-api` pod.

`istioctl proxy-config all deploy/web-api -n istioinaction -o json`

## Question

If you have only deployed a few services, why is there so much Envoy configuration detail for the pod? The reason is that Istio listens to everything in your Kubernetes cluster by default. You can explore using discovery selectors, and `exportTo` and `Sidecar` resources for operating Istio in a production environment in the Istio Essentials and Expert workshops from Solo.io.

## Next lab

Congratulations, you have enabled strict mTLS policy for all services in the entire Istio mesh. We'll explore controlling traffic with these services in the next lab.