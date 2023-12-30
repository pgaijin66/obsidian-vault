

```bash
nameserver 10.3.0.10
search example.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
```

The strategy is that the resolution of a name, for instance `my-service` or `www.google.com`, will be tested against each of the domains specified in the `search` directive: here for the examples, it would be the FQDN chains `my-service.example.svc.cluster.local`,`my-service.svc.cluster.local`,`my-service.cluster.local`,`my-service` and `www.google.com.example.svc.cluster.local`,`www.google.com.svc.cluster.local`,`www.google.com.cluster.local`,`www.google.com`. Here obviously it would be the 1st FQDN of the first chain (`my-service.example.svc.cluster.local`) and the last FQDN of the second chain (`www.google.com`) that would be resolved correctly.

One can see that this strategy is made to optimize resolution of internal name of the cluster, in a way that allows names like `my-service`, `my-service.my-namespace` or `my-service.my-namespace.svc` to be nicely resolved out-of-the-box.

The `[[ndots]]` parameter in the `options` directive defines the minimum number of dots in a name to consider that a name is actually a FQDN and so the search chain should be skiped in favor of a direct DNS resolution attempt. With `ndots:2`, `www.google.com` will be considered as a FQDN while `my-service.my-namespace` will go through the search chain.

Given that the `search` option over 3 possible domains, that any obvious URL will not be considered as a FQDN because of `ndots:5` and the break of the search loop in `musl` library in Alpine docker, all of this dramatically increases the probability of a host resolution failure in a Docker Alpine running in Kubernetes. If your host resolution is part of some kind of a loop running regularly, you will encounter a lot of failures that need to be handled.

Tags:
[[ndots]] #dns #ndots