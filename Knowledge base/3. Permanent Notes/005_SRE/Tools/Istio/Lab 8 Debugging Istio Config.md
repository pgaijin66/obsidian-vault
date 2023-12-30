The service mesh contains proxies that are on the request path between services. When anomalies are detected, it's typically because of a misconfiguration. In this lab, we explore tools to troubleshoot misconfiguration and get a better understanding of how to debug Istio.

## istioctl analyze

The istioctl CLI tool contains a few useful tools to help sanity-check configuration. For example, run the following:

`istioctl analyze`

This will output a set of reports about the state of your Istio configuration; you should see something like this:

`Warning [IST0103] (Pod envoy-6cc79f5b67-f5dqz.default) The pod is missing the Istio proxy. This can often be resolved by restarting or redeploying the workload. Info [IST0118] (Service envoy.default) Port name admin (port: 15000, targetPort: 15000) doesn't follow the naming convention of Istio port.`

You can safely ignore the warning.

Please see the Istio documentation about istioctl analyze for more.

## istioctl x describe

With a lot of different configurations affecting the edge between two services, it can be helpful to ask Istio "what configurations apply to a workload". You can do exactly that with the istioctl x describe command. For example, to see what configurations apply to the web-api workload, run the following:

`istioctl x describe service web-api -n istioinaction`

In our simple environment, we can see the following rules:

`Service: web-api    Port: http 8080/HTTP targets pod port 8080 RBAC policies: ns[istioinaction]-policy[audit-web-api-authpolicy]-rule[0] Skipping Gateway information (no ingress gateway pods)`

This command can make it apparent when a configuration you intended to affect service behavior is present/not present and give you indications about which resources to review.

Please see the Istio documentation about istioctl x describe for more.

## istioctl proxy-status

The istioctl proxy-status command can be used to get a quick overview about whether configuration from the control plane to the data plane has been sync'd, what istiod control plane a workload considers its source of truth, and what version of the proxy a workload is running. For example:

`istioctl proxy-status`

Here we can see all of the workloads in the mesh with useful details:

`NAME                                                   CDS        LDS        EDS        RDS        ISTIOD                           VERSION httpbin-9d9dbcd4-xr8tw.default                         SYNCED     SYNCED     SYNCED     SYNCED     istiod-1-16-84c6b6cdc7-ztj84     1.16.0 istio-ingressgateway-5bc557575f-dsc2c.istio-ingress    SYNCED     SYNCED     SYNCED     SYNCED     istiod-1-16-84c6b6cdc7-ztj84     1.16.0 purchase-history-v1-54c8956877-s79qn.istioinaction     SYNCED     SYNCED     SYNCED     SYNCED     istiod-1-16-84c6b6cdc7-ztj84     1.16.0 recommendation-7f66565d54-l2d4t.istioinaction          SYNCED     SYNCED     SYNCED     SYNCED     istiod-1-16-84c6b6cdc7-ztj84     1.16.0 sleep-854565cb79-krhks.istioinaction                   SYNCED     SYNCED     SYNCED     SYNCED     istiod-1-16-84c6b6cdc7-ztj84     1.16.0 web-api-5d56f44d8b-b7pxm.istioinaction                 SYNCED     SYNCED     SYNCED     SYNCED     istiod-1-16-84c6b6cdc7-ztj84     1.16.0`

The most powerful part of the `proxy-status` command is being able to detect drift between the control plane and data plane configurations. For example...

`istioctl proxy-status deploy/web-api -n istioinaction`

`Clusters Match Listeners Match Routes Match (RDS last loaded at Thu, 04 Mar 2021 08:05:33 MST)`

## istioctl proxy-config

As we've seen in previous labs, the istioctl proxy-config command can be used to query the proxy for its configuration for particular elements. You can see the Istio docs for more information. In this lab we will cover two critical parts of the istioctl proxy-config command: data plane configuration and data plane logging.

Config

The istioctl proxy-config command allows you to review the data plane configuration which effectively comes from the /config_dump endpoint on Envoy's admin module. We can go right to the admin API with the following command for the sleep deployment:

`istioctl dashboard envoy deploy/sleep --address 0.0.0.0`

Then select the Envoy dashboard tab which gives access to things like `/stats`, `/server_info`, and the full `/config_dump`. Another important endpoint is the `/clusters` endpoint which gives detailed information for all of the Envoy clusters including the specific endpoints that make up a cluster. You can also see endpoint information (EDS) in the full config dump with `/config_dump?include_eds`.

As an exercise for the reader, try to run the istioctl dashboard envoy command and explore these different endpoints

Logging

Envoy proxy can be configured to increase logging levels to a very fine-grained "debug" level which will help you understand more about what's happening on the data plane. Envoy also categorizes different modules for logging, so you can enable/disable logging for just the components you need to review.

For example, to turn on debug logging for a particular pod:

`istioctl proxy-config log deploy/web-api -n istioinaction --level debug`

To configure just a specific module for debug:

`istioctl proxy-config log deploy/web-api -n istioinaction --level info istioctl proxy-config log deploy/web-api -n istioinaction --level connection:debug,conn_handler:debug,filter:debug,router:debug,http:debug,upstream:debug`

The following logging levels are available:

-   trace
-   debug
-   info
-   warning
-   error
-   critical
-   off

## Additional health and monitoring endpoints

Istio provides a nice `debug` interface on the Istio control plane. We can call it with the convenience command `pilot-discovery` like we did in a previous lab. We could also call it from `curl` or any HTTP client, for example:

`kubectl exec deploy/istiod-${ISTIO_REVISION} -n istio-system -- curl localhost:15014/debug/registryz | jq`

See the Istio docs for a full list of debugging paths.

One example is to get the EDS debug info for the httpbin service proxy:

`export HTTPBIN_ID=$(kubectl get pod -l app=httpbin -o jsonpath='{.items[0].metadata.name}') kubectl exec deploy/istiod-${ISTIO_REVISION} -n istio-system -- curl localhost:15014/debug/edsz?proxyID=$HTTPBIN_ID | jq`

To get Prometheus metrics for the istiod control plane:

`kubectl exec deploy/istiod-${ISTIO_REVISION} -n istio-system -- curl localhost:15014/metrics -s`

To get Istiod version:

`kubectl exec deploy/istiod-${ISTIO_REVISION} -n istio-system -- curl localhost:15014/version -s`

## Recap

This only scratches the surface on the tools at your disposal to help you debug any potential problems that arise and it should behoove you to go to the Istio Docs and learn plenty more. Hopefully these tools will be your savior in identifying malicous bugs in a reasonable amount of time.

## The End

With that note, congrats on completing all eight labs in the Deploying Istio Workshop. We hope that you found this information useful, insighful, and you are looking foward to using these tools in the future.