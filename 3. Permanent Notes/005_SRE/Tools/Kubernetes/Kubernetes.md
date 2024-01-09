Type: #MOC
Tags: #kubernetes
# Kubernetes

1. Deployments
	1. Stateless application
	2. Scaling
2. StatefulSets
	1. Stateful application ( stable network identities, persistent storage )
	2. Pod have a unique identity
3. DaemonSets
4. Ingresses vs loadbalancer
	1. Kubernetes Ingress: L7  LB, but also, routing, TLS termination, name-based routing, path based routing
	2. Loadbalancer: Auto provisions cloud LB, TCP/UDP load balancing, L4
5. Service
	6. ClusterIP
	7. Loadbalancer
	8. NodePort
6. Topology aware routing
7. CRDs
8. Persistent Volume
	1. Is not deleted as name suggest it is persistent volume
9. Persistent Volume claims:
	1. Is not deleted when pod is deleted, based on retention policy
10. Init containers
11. Patterns
	1. Side car pattern ( istio-proxy, Log collection and forwarding, metrics, service mesh i.e istio-proxy for security and traffic management, )
	2. ambassador pattern
	3. Proxy or adapter
12. CNI
13. CRI: Interface which standardizes who k8s interacts with container runtime,
14. CRI-O: CRI-O is a container runtime for Kubernetes. It implements CRI.
15. OCI: How container should be built
16. Service mesh:
	1. Ingress
		1. Ingress gateway
		2. Secure gateway
		3. TLS termination
		4. 
	2. Traffic management
		1. LB
		2. Traffic shifting and splitting
	3. Observability
	4. Security ( mTLS, Authentication and Authorization i.e JWT)
	5. DNS and Certificate management ( external dns and cert manager )
	6. Service discovery
	7. Resiliency ( Request timeouts, Retries, Rate limiting, circuit breaking)
	8. Multi cluster networking
	9. Platform agnostic
	10. Fault injection and testing
		1. Chaos testing
17. Istio
	1. Enable istio injection so that istio can inject sidecar container i.e envoy proxy
	2. Control plane: Manages data plane
	3. Data place Group of envoy proxies injected into k8s pods which act as sidecar proxy
	4. Components:
		1. Pilot
		2. Citadel
		3. Gallley
		4. Mixer
	5. Istio profiles
		1. Default, demo, minimal, remote, emtry
	6. Observability
		1. Jeager
		2. Kiali
		3. Zipkin
	7. Gotcha: Does not like resource quota to applied to namespace, Sidecar taking a lot of memory ( assuming it needs to talk with everyone i.e everything pilot taking too much, metadata );solution: limit information by scoping only allowed namespace 
18. RBAC
	1. Roles / RoleBindings
	3. ClusterRole / ClusterRoleBinding