Getting started with Envoy based technologies is best by starting small and iteratively growing. In this lab we will take a look at adopting Envoy at the edge with the Istio ingress gateway. The intention of the ingress gateway is to allow traffic into the mesh. If you need more sophisticated edge gateway capabilities (rate limiting, request transformation, OIDC, LDAP, OPA, etc) then use a gateway specifically built for those use cases, such as Gloo Edge.

## Prerequisites

Verify you're in the correct folder for this lab: `/root/istio-workshops/deploy-istio-to-production`. This lab builds on both lab 02 and 03 where we already installed the Istio control plane using a minimal profile and using revisions.

## Install Istio ingress gateway

We will continue the approach of using separate installation files for Istio components. We will install the ingress gateway using the following approach. This allows us to separate upgrades/changes to the control plane from the ingress gateway. Since the ingress gateway is likely taking production traffic, we want to treat it separately from other components. Let's install it using the following approach:

`cat labs/04/ingress-gateways.yaml`

`apiVersion: install.istio.io/v1alpha1 kind: IstioOperator metadata:   name: istio-ingress-gw-install spec:   profile: empty  values:     gateways:       istio-ingressgateway:         autoscaleEnabled: false   components:     ingressGateways:     - name: istio-ingressgateway       namespace: istio-ingress       enabled: true       k8s:         overlays:         - apiVersion: apps/v1          kind: Deployment          name: istio-ingressgateway           patches:           - path: spec.template.spec.containers[name:istio-proxy].lifecycle             value:                preStop:                  exec:                    command: ["sh", "-c", "sleep 5"]         service:           type: LoadBalancer          ports:             - port: 80               targetPort: 8080               name: http2            - port: 443               targetPort: 8443               name: https`

Note that this uses the empty profile and enables the istio-ingressgateway component.

Let's install it with a revision that matches the control plane in the istio-ingress namespace. We recommend that you install the istio-ingress gateway in a namespace that is different than istiod for better security and isolation.

`kubectl create namespace istio-ingress istioctl install -y -n istio-ingress -f labs/04/ingress-gateways.yaml --revision ${ISTIO_REVISION}`

We should check that the ingress gateway was correctly installed:

`kubectl get po -n istio-ingress`

`NAME                                    READY   STATUS    RESTARTS   AGE istio-ingressgateway-5686db779c-8nr5p   1/1     Running   0          78s`

The ingress gateway will create a Kubernetes Service of type LoadBalancer. Use this IP address to reach the gateway:

`kubectl get svc -n istio-ingress`

`NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                                                      AGE istio-ingressgateway   LoadBalancer   10.44.0.91     35.202.132.20   15021:32218/TCP,80:30062/TCP,443:30105/TCP,15012:32488/TCP,15443:30178/TCP   5m45s`

We use the GATEWAY_IP environment variable in other parts of this lab so lets set it now:

`GATEWAY_IP=$(kubectl get svc -n istio-ingress istio-ingressgateway -o jsonpath="{.status.loadBalancer.ingress[0].ip}")`

## Expose our apps

Even though we don't have our apps in the istioinaction namespace in the mesh yet, we can still use the Istio ingress gateway to route traffic to them. Let's apply a Gateway and VirtualService resource to permit this:

`kubectl -n istioinaction apply -f sample-apps/ingress/`

The ingress gateway will create new routes on the proxy that we should be able to call:

`curl -H "Host: istioinaction.io" http://$GATEWAY_IP`

We can query the gateway configuration using the istioctl proxy-config command:

`istioctl proxy-config routes deploy/istio-ingressgateway.istio-ingress`

`NOTE: This output only contains routes loaded via RDS. NAME        DOMAINS              MATCH                  VIRTUAL SERVICE http.8080   istioinaction.io     /*                     web-api-gw-vs.istioinaction             *                    /stats/prometheus*             *                    /healthz/ready*`

If we wanted to see an individual route, we can ask for its output as json like this:

`istioctl proxy-config routes deploy/istio-ingressgateway.istio-ingress --name http.8080 -o json`

## Securing inbound traffic with HTTPS

To secure inbound traffic with HTTPS, we need a certificate with the appropriate SAN. Let's create one for istioinaction.io:

`kubectl create -n istio-ingress secret tls istioinaction-cert --key labs/04/certs/istioinaction.io.key --cert labs/04/certs/istioinaction.io.crt`

We can update the gateway to use this cert:

`cat labs/04/web-api-gw-https.yaml`


```
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
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "istioinaction.io"
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

`apiVersion: networking.istio.io/v1beta1 kind: Gateway metadata:   name: web-api-gateway   namespace: istioinaction spec:   selector:     istio: ingressgateway  servers:   - port:       number: 80       name: http      protocol: HTTP    hosts:     - "istioinaction.io"   - port:       number: 443       name: https      protocol: HTTPS    hosts:     - "istioinaction.io"     tls:       mode: SIMPLE      credentialName: istioinaction-cert`

Note that we are pointing to the istioinaction-cert, and that the cert must be in the same namespace as the ingress gateway deployment. Even though the Gateway resource is in the istioinaction namespace, the cert must be where the gateway is actually deployed.

Now lets apply it:

`kubectl -n istioinaction apply -f labs/04/web-api-gw-https.yaml`

Example calling it:

`curl --cacert ./labs/04/certs/ca/root-ca.crt -H "Host: istioinaction.io" https://istioinaction.io:443 --resolve istioinaction.io:443:$GATEWAY_IP`

What are some common issues that folks run into with this approach?

-   Users may not have access to write anything (ie, certs) into istio-ingress
-   Users may not manage their own certs
-   Integration with CA/PKI is highly desirable

Let's dig into these a bit.

Let's delete the secret we created earlier and see what other options we have:

`kubectl delete secret -n istio-ingress istioinaction-cert`

Note, we should delete the secret like in the previous step so the next sections will work as expected

## Integrate Istio ingress gateway with Cert Manager

In the previous section we already had a cert for our domain. In this section, let's use cert-manager to provision the certs for us using a backend CA. In this lab, the CA will be our own CA but cert-manager can be integrated with a lot of backend PKI --- which is a big reason why cert-manager is so popular.

Let's prep for the installation of cert manager:

`kubectl create namespace cert-manager helm repo add jetstack https://charts.jetstack.io helm repo update`

Now do the actual installation:

`helm install cert-manager jetstack/cert-manager --namespace cert-manager --version ${CERT_MANAGER_HELM_VERSION} --create-namespace --set installCRDs=true --wait`

Verify things installed correctly:

`kubectl get po -n cert-manager`

`NAME                                       READY   STATUS    RESTARTS   AGE cert-manager-85f9bbcd97-zslwx              1/1     Running   0          2m45s cert-manager-cainjector-74459fcc56-xq6pk   1/1     Running   0          2m45s cert-manager-webhook-57d97ccc67-xtl82      1/1     Running   0          2m45s`

You should wait a few seconds till all the pods are running

Since we're going to use our own CA as the backend, let's install the correct root certs/keys:

`kubectl create -n cert-manager secret tls cert-manager-cacerts --cert labs/04/certs/ca/root-ca.crt --key labs/04/certs/ca/root-ca.key`

Note that this is just for the lab. Ideally if you use cert-manager, you'll be using Vault, LetsEncrypt, or your own PKI.

Let's configure a ClusterIssuer to use our CA:

`cat labs/04/cert-manager/ca-cluster-issuer.yaml`

`apiVersion: cert-manager.io/v1 kind: ClusterIssuer metadata:   name: ca-issuer   namespace: sandbox spec:   ca:     secretName: cert-manager-cacerts`

Lets add it:

`kubectl apply -f labs/04/cert-manager/ca-cluster-issuer.yaml`

We will ask ask cert-manager to issue us a secret with this config:

`cat labs/04/cert-manager/istioinaction-io-cert.yaml`

```
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: istioinaction-cert
  namespace: istio-ingress
spec:
  secretName: istioinaction-cert
  duration: 2160h # 90d
  renewBefore: 360h # 15d
  subject:
    organizations:
    - solo.io
  isCA: false
  privateKey:
    algorithm: RSA
    encoding: PKCS1
    size: 2048
  usages:
    - server auth
    - client auth
  dnsNames:
  - istioinaction.io  
  issuerRef:
    name: ca-issuer
    kind: ClusterIssuer
    group: cert-manager.io
```


After reviewing the config, go ahead and apply it:

`kubectl apply -f labs/04/cert-manager/istioinaction-io-cert.yaml`

Let's make sure the certificate was recognized and issued:

`kubectl get Certificate -n istio-ingress`

`NAME                 READY   SECRET               AGE istioinaction-cert   True    istioinaction-cert   12s`

Let's check the certificate SAN to verify it was specified correctly as istioinaction.io:

`kubectl get secret -n istio-ingress istioinaction-cert -o jsonpath="{.data['tls\.crt']}" | base64 -d | step certificate inspect -`

Let's try call our gateway again to make sure the call still succeeds:

`curl -vs --cacert ./labs/04/certs/ca/root-ca.crt -H "Host: istioinaction.io" https://istioinaction.io:443 --resolve istioinaction.io:443:$GATEWAY_IP`

In this section, we created the Certificate in the istio-ingress namespace. But if we don't have access to that namespace, what else could we do?

## Reduce Gateway Config for large meshes

By default, the ingress gateways will be configured with information about every service in the mesh and in fact every service that Istio's control plane has discovered. This is likely overkill for most large mesh deployments. With the gateway, we can scope down the number of backend services that get configured on the gateway to only those that have routing rules defined for them.

For example, in our current status with the gateway, let's see what "clusters" it knows about:

`istioctl pc clusters deploy/istio-ingressgateway -n istio-ingress`

Note that "clusters" is referring to Envoy clusters, not Kubernetes clusters. A Envoy cluster is a group of logically similar upstream hosts that Envoy connects to.

As you see, the output here is quite extensive and includes clusters that the gateway does not need to know anything about. The only clusters that get traffic routed to it from the gateway are the web-api cluster. Let's configure the control plane to scope this down. To do that, we set the PILOT_FILTER_GATEWAY_CLUSTER_CONFIG environment variable in the istiod deployment:

`istioctl install -y -n istio-system -f labs/04/control-plane-reduce-gw-config.yaml --revision ${ISTIO_REVISION}`

Give a few moments for istiod to come back up. Then run the following to verify the setting PILOT_FILTER_GATEWAY_CLUSTER_CONFIG took effect:

`kubectl get deploy/istiod-${ISTIO_REVISION} -n istio-system -o jsonpath="{.spec.template.spec.containers[].env[?(@.name=='PILOT_FILTER_GATEWAY_CLUSTER_CONFIG')]}";`

You should see something like this:

`{"name":"PILOT_FILTER_GATEWAY_CLUSTER_CONFIG","value":"true"}`

Let's check the ingress gateway again:

`istioctl pc clusters deploy/istio-ingressgateway -n istio-ingress`

You should see a much more slimmed down list including the web-api.istioinaction.svc.cluster.local cluster. There are a couple of additional clusters that have been configured as static clusters.

## Access logging for gateway

In this last section of this lab, we will see how to enable access logging for the ingress gateway. Access logging is instrumental in understanding what traffic is coming and what are the results of that traffic. Istio's documentation shows how to enable access logging but it's for the entire mesh. In this section, we will enable access logging for just the ingress gateway. The UX around this is continuously improving, so in the future there may be an easier way to do this.

Let's take a look at the configuration we'll use to configure access logging for the ingress gateway:

`cat labs/04/ingress-gw-access-logging.yaml`

```
apiVersion: networking.istio.io/v1alpha3
kind: EnvoyFilter
metadata:
  name: ingressgateway-access-logging
  namespace: istio-ingress
spec:
  workloadSelector:
    labels:
      istio: ingressgateway
  configPatches:
  - applyTo: NETWORK_FILTER
    match:
      context: GATEWAY
      listener:
        filterChain:
          filter:
            name: "envoy.filters.network.http_connection_manager"
    patch:
      operation: MERGE
      value:
        typed_config:
          "@type": "type.googleapis.com/envoy.extensions.filters.network.http_connection_manager.v3.HttpConnectionManager"
          access_log:
          - name: envoy.access_loggers.file
            typed_config:
              "@type": "type.googleapis.com/envoy.extensions.access_loggers.file.v3.FileAccessLog"
              path: /dev/stdout
              format: "[%START_TIME%] \"%REQ(:METHOD)% %REQ(X-ENVOY-ORIGINAL-PATH?:PATH)% %PROTOCOL%\" %RESPONSE_CODE% %RESPONSE_FLAGS% \"%UPSTREAM_TRANSPORT_FAILURE_REASON%\" %BYTES_RECEIVED% %BYTES_SENT% %DURATION% %RESP(X-ENVOY-UPSTREAM-SERVICE-TIME)% \"%REQ(X-FORWARDED-FOR)%\" \"%REQ(USER-AGENT)%\" \"%REQ(X-REQUEST-ID)%\" \"%REQ(:AUTHORITY)%\" \"%UPSTREAM_HOST%\" %UPSTREAM_CLUSTER% %UPSTREAM_LOCAL_ADDRESS% %DOWNSTREAM_LOCAL_ADDRESS% %DOWNSTREAM_REMOTE_ADDRESS% %REQUESTED_SERVER_NAME% %ROUTE_NAME%\n"
```


You can see we are using an EnvoyFilter resource to affect the configuration of the gateway proxy. Let's apply this resource:

`kubectl apply -f labs/04/ingress-gw-access-logging.yaml`

Now send some traffic through the ingress gateway.

`curl --cacert ./labs/04/certs/ca/root-ca.crt -H "Host: istioinaction.io" https://istioinaction.io:443 --resolve istioinaction.io:443:$GATEWAY_IP`

After sending some traffic through the gateway, check the logs:

`kubectl logs -n istio-ingress deploy/istio-ingressgateway -c istio-proxy`

You should see something like the following access log:

`[2021-03-02T20:43:27.195Z] "GET / HTTP/2" 200 - "-" 0 1102 11 11 "10.128.0.61" "curl/7.64.1" "8821b96b-ecab-4303-9f6d-11681ee22e8f" "istioinaction.io" "10.40.2.49:8080" outbound|8080||web-api.istioinaction.svc.cluster.local 10.40.0.43:32838 10.40.0.43:8443 10.128.0.61:53829 istioinaction.io -`

## Recap

We covered a lot in this lab about Gateways, but we're just scratching the surface. We touched on common Gateway use cases including getting traffic into the mesh and routing to services that may be in the cluster but may not be in the mesh yet. Some other cases we touched on:

1.  Users may not have access to write anything (ie, certs) into the istio-ingress namespace
2.  Users may not manage their own certs
3.  Integration with CA/PKI is highly desirable

## Next lab

Congrats, we have added a gateway and have secured it using a cert manager. In the next lab, we take a look at iteratively and safely getting workloads into the mesh. tabs:
