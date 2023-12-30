
## Useful commands
Your friends
```
context
kubectl config get-contexts -o name > /opt/course/1/contexts
kubectl config

Run commands in container

kubectl exec -h
kubectl -n NS exec POD_NAME -- commands curl, nslookup
k -n NS run PODNAME --image=nginx:alpine --rm -i sh -c 'COMMANDS'

Create a new service
kubectl expose -h
kubectl -n NS expose deploy DEPLOYMENT_NAME --port NUMBER

Scale pods
kubectl scale -h

Show labels
kubectl get pod -L trust

Create new service account
kubectl create sa -h
kubectl create sa demoUser

Create service account token
kubectl create token -h
kubectl create token TOKENNAME => This is emphemeral ( shown once )

Port forward
kubectl port-forward -h

Manage rollout or rollback
kubectl rollout -h

Check
kubectl -oyaml
kubectl -oyaml --dry-run=

Watch container process
watch crictl ps

Create role
kubectl create role secret-manager --verb=get --resource=secrets -o yaml --dry-run=client

Create RoleBinding
kubectl create rolebinding secret-manager --role=secret-manager --user=jane -oyaml --dry-run=client

Test
kubectl auth can-i -h
kubectl auth can-i get secrets --as jane
kubectl auth can-i delete secrets --as jane

Key
openssl genrsa -out jane.key 2048

generate csr
openssl req -new -key jane.key -out jane.csr

Shell
kubectl exec -it pod_name -it -- bash

Test service account
kubectl auth delete secrets --as system:serviceaccount:NAMESPACE:SA_NAME

Disable anonymous access
kube-apiserver --anonymous-auth=false
kube-apiserver --insecure-port=8080

Edit service
kubectl edit svc kubernetes

Checking logs
/var/log/pods
/var/logs/containers
crictl ps
crictl logs COntainerID
journalctl | grep apiserver
cat /var/log/syslog | grep apiserver


create secret
kubectl create secret -h
kubectl create secret generic sec1 --from-literal pass=123
kubectl create secret generic sec2 --from-frile index=/etc/hosts

replace pod forcefully
kubectl -f pod.yaml replace --force
kubectl delete pod NAME --force --grace-period=0
```

#### Task 1 - Running two container is the same PID namespace
`docker run --name app1 -d nginx:alpine sh -c 'sleep infinity'`

`docker run --name app2 --pid=container:app1 -d nginx:alpine sh -c 'sleep infinity'`

`podman run --name app2 --pid=container:app1 -d nginx:alpine sh -c 'sleep infinity'`


Verify
```go
docker exec app1 ps aux
```



## Cluster setup: Network Policies

- They are basically firewall rules
- Implemented using CNI ( calico / weave )
- Applied at namespace level
- selectors:
	- namespaceSelector
	- podSelector
	- ipBlock
- policyType
	- ingress
	- Egress
- rules
	- union of all rules
	- order does not matter
- pod selector is used to select certain group of pods based on label and NP rules will be applied to those pods
- policyType / Egress = Allow egress from pod selector pods to egress pods
- namespace selector = allow incoming traffic from 
- In exam make sure, that when you apply default deny, DNS is allowed

Tasks
- [ ] Create a default deny for all egress
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-out
  namespace: app
spec:
  podSelector: {}
  policyTypes:
  - Egress
  egress:
  - ports:
    - port: 53
      protocol: TCP
    - port: 53
      protocol: UDP
```
- [ ] Allow frontend pods to talk to backend pods 
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: np
  namespace: space1
spec:
  podSelector: {}
  policyTypes:
  - Egress
  egress:
  - to:
     - namespaceSelector:
        matchLabels:
         kubernetes.io/metadata.name: space2
  - ports:
    - port: 53
      protocol: TCP
    - port: 53
      protocol: UDP
```


```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: np
  namespace: space2
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  ingress:
   - from:
     - namespaceSelector:
        matchLabels:
         kubernetes.io/metadata.name: space1
```


Verify

```
# these should work
k -n space1 exec app1-0 -- curl -m 1 microservice1.space2.svc.cluster.local
k -n space1 exec app1-0 -- curl -m 1 microservice2.space2.svc.cluster.local
k -n space1 exec app1-0 -- nslookup tester.default.svc.cluster.local
k -n kube-system exec -it validate-checker-pod -- curl -m 1 app1.space1.svc.cluster.local

# these should not work
k -n space1 exec app1-0 -- curl -m 1 tester.default.svc.cluster.local
k -n kube-system exec -it validate-checker-pod -- curl -m 1 microservice1.space2.svc.cluster.local
k -n kube-system exec -it validate-checker-pod -- curl -m 1 microservice2.space2.svc.cluster.local
k -n default run nginx --image=nginx:1.21.5-alpine --restart=Never -i --rm  -- curl -m 1 microservice1.space2.svc.cluster.local
```

- [ ] Allow backend pods to talk to database pods
- [ ] Block metadata ( add Network policy to allow all egress traffic except to metadata server i.e 169)
	- [ ] Only allow some pod with certain label to access the endpoint


## Cluster Setup: Kubernetes dashboard
- Tesla hack
	- kubernetes dashboard
- Only expose externally if needed and protected
- Restrict using RBAC
- enforce using authentication
- change arguments


## Cluster Setup: Secure Ingress
- node port service makes a cluster Ip service accessible from outside by opening port on each node
- The _Ingress_ resources needs to be created in the same _Namespace_ as the applications.
- TASK
	- [ ] Creating ingress for existing services
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: world
  namespace: world
  annotations:
    # this annotation removes the need for a trailing slash when calling urls
    # but it is not necessary for solving this scenario
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx # k get ingressclass
  rules:
  - host: "world.universe.mine"
    http:
      paths:
      - path: /europe
        pathType: Prefix
        backend:
          service:
            name: europe
            port:
              number: 80
      - path: /asia
        pathType: Prefix
        backend:
          service:
            name: asia
            port:
              number: 80
```



## Cluster Setup: Node Metadata protection
- Use network policy to deny access to VM metadata
- Each VM in cloud can communicate with metadata servers
- [ ] only allow pods with certain label to access the endpoint

Deny all first
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: cloud-metadata-deny
  namespace: default
spec:
  podSelector: {}
  policyTypes:
  - Egress
  egress:
  - to:
    - ipBlock:
        cidr: 0.0.0.0/0
        except:
	        - 169.254.169.254/32
```


Allow only with certain labels
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: cloud-metadata-deny
  namespace: default
spec:
  podSelector:
	  matchLabels:
		  role: metadata-accessor
  policyTypes:
  - Egress
  egress:
  - to:
    - ipBlock:
        cidr: 169.254.169.254/32
```

- [ ] Create the NP where we allow traffic to all addresses, except the evil one

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: metadata-server
  namespace: default
spec:
  podSelector:
    matchLabels:
      trust: nope
  policyTypes:
  - Egress
  egress:
  - to:
    - ipBlock:
        cidr: 0.0.0.0/0
        except:
          - 1.1.1.1/32
```


Test
```
# these should work
k exec trust-0 -- nc -v 1.1.1.1 53
k exec trust-0 -- nc -v -w 1 www.google.de 80
k exec no-trust-0 -- nc -v -w 1 www.google.de 80

# these should not work
k exec no-trust-0 -- nc -v 1.1.1.1 53
```



## Cluster Setup: CIS benchmark
- Center for Internet Security
- kube-bench
	- See all : `kube-bench run --targets master`
	- Check against a single rule `kube-bench run --target masters --check 1.2.20`
- [ ] Ensure 1.2.20 has pass
```
kube-bench run targets master --check 1.2.20
```
- [ ] Ensure 1.3.2 has a status PASS
```
kube-bench run targets master --check=1.3.2
```
- docker bench
	- 


## Cluster Setup: Verify Platform Binaries
- use `sha512sum BIN` to verify binary signature

## Cluster Hardening: RBAC
- Namespaced resources
	- Role: What can an actor do ?
	- RoleBinding: Who can perform ?
- Non namespaced resources
	- ClusterRole: Applied to whole cluster
	- ClusterRoleBinding: Applied to whole cluster
- Accounts
	- service account: used to communicate 
	- User: cert and key
		- signed by CA
- RBAC combination:
	- Role + RoleBinding: 
		- Available in a single namespace
		- Applied to single namespace
	- Cluster Role + Cluster RoleBinding
		- Available across the cluster in all namespace
		- Applied across the cluster across all namespace
	- Cluster Role + RoleBinding
		- role available across the cluster
		- applied in a single namespace
	- Role + Cluster RoleBinding
		- Not possible

##### Task
There are existing _Namespaces_ `ns1` and `ns2` .

Create _ServiceAccount_ `pipeline` in both _Namespaces_.

These _SAs_ should be allowed to view almost everything in the whole cluster. You can use the default _ClusterRole_ `view` for this.

These _SAs_ should be allowed to create and delete _Deployments_ in their _Namespace_.

##### Verify everything using `kubectl auth can-i` .


```bash
# create Namespaces
k -n ns1 create sa pipeline
k -n ns2 create sa pipeline

# use ClusterRole view
k get clusterrole view # there is default one
k create clusterrolebinding pipeline-view --clusterrole view --serviceaccount ns1:pipeline --serviceaccount ns2:pipeline

# manage Deployments in both Namespaces
k create clusterrole -h # examples
k create clusterrole pipeline-deployment-manager --verb create,delete --resource deployments
# instead of one ClusterRole we could also create the same Role in both Namespaces

k -n ns1 create rolebinding pipeline-deployment-manager --clusterrole pipeline-deployment-manager --serviceaccount ns1:pipeline
k -n ns2 create rolebinding pipeline-deployment-manager --clusterrole pipeline-deployment-manager --serviceaccount ns2:pipeline
```


#### Test using `can-i`
```
```bash
# namespace ns1 deployment manager
k auth can-i delete deployments --as system:serviceaccount:ns1:pipeline -n ns1 # YES
k auth can-i create deployments --as system:serviceaccount:ns1:pipeline -n ns1 # YES
k auth can-i update deployments --as system:serviceaccount:ns1:pipeline -n ns1 # NO
k auth can-i update deployments --as system:serviceaccount:ns1:pipeline -n default # NO

# namespace ns2 deployment manager
k auth can-i delete deployments --as system:serviceaccount:ns2:pipeline -n ns2 # YES
k auth can-i create deployments --as system:serviceaccount:ns2:pipeline -n ns2 # YES
k auth can-i update deployments --as system:serviceaccount:ns2:pipeline -n ns2 # NO
k auth can-i update deployments --as system:serviceaccount:ns2:pipeline -n default # NO

# cluster wide view role
k auth can-i list deployments --as system:serviceaccount:ns1:pipeline -n ns1 # YES
k auth can-i list deployments --as system:serviceaccount:ns1:pipeline -A # YES
k auth can-i list pods --as system:serviceaccount:ns1:pipeline -A # YES
k auth can-i list pods --as system:serviceaccount:ns2:pipeline -A # YES
k auth can-i list secrets --as system:serviceaccount:ns2:pipeline -A # NO (default view-role doesn't allow)
```
```
```


##### Task
Create a new user that can communicate with k8s. 
1. Create a new KEY at `/root/60099.key` for user named `60099@internal.users`
2. Create a CSR at `/root/60099.csr` for the KEY


##### Solution
```
openssl genra -out 60099.key 2048
opessn req -new -key 60099.key -out 60099.csr
```




## Cluster Hardening: Service account
- SA are namespace bound
- Each SA is given a token
- Disable the auto mount of SA token in pod 
	- In SA: `automountServiceAccountToken: false`
	- In Pod: below spec `automountServiceAccountToken: false`


### Task
- Create a service account and use it in a pod
- Use service account to connect to API from inside the pod


## Cluster Hardening: restrict API access
- API requests are always tied to
	- normal user
	- service account
	- anonymous
- Every request must authenticate
- Restrictions
	- Do not allow anonymous access
	- Close insecure port
	- Do not expose API server to outside
	- Restrict access from Node to API
	- Preventing unauthorized access ( RBAC )
	- Prevent pods from accessing API ( Prevent mounting )
	- APIserver port behind firewall
- Insecure access
	- `kube-apiserver --insecure-port=8080`
- Node restriction admission controller
	- 





---

Apparmor

create pod which uses apparmour

`kubectl run secure --image=nginx -oyaml --dry-run=client`


Seccomp

`docker run --security-opt seccomp=default.json nginx`

create nginx pod in k8s and assign a secomp profile to it

move seccomp to the profiole location in master

1. create pod

``
`lsof -i  :21`

`netstat -pltn` | grep ftp


`systemctl list-units --type service | grep ftp`

`systemctl stop | restart | start`


`run pod`

`kubectl run PODNAME --image nginx -oyaml --dry-run=client`

`run service`


`run deployment`


strace
`strace -cw ls /`

follow subprocess or forks

`strace -cw -p PID -f `


Grep 10 lines before and 10 lines after match

`grep 1234 -A10 -B10`

ETCD_CTL




### List containers in k8s
`crictl ps`

## Cluster Hardening: Upgrade kubernetes

Why upgrade frequently ?
- support, security fix, bug fixes, stay up to date for dependencies

How to upgrade cluster
- First upgrade the master component ( API server, controller-manager, scheduler )
- Then the worker component ( kubelet, kube-proxy )
- Components same minor version as api server

How to upgrade
- `kubectl drain`
	- safely evict all pods from node
	- Mark node as SchedulingDisabled (`kubectl cordon`)
- Do the upgrade
- `kubectl uncordon`

How to make application can survive upgrade
- pod grace period / terminating state
- pod lifecycle events
- pod disruption 


Upgrade control plane
`kubectl get nodes`
`kubectl drain cks-controlplane --ignore-daemonsets
check using `kubectl get nodes`
`apt-get update` to get latest version
`apt-cache show kubeadm | grep 1.22`
`apt-mark hold kubectl kubelet` hold this packages
`apt-unmark kubeadm` unmark
`apt-get install kubeadm=1.22.5-0`
`kubeadm version`
`kubeadm upgrade plan` to check possible upgrade
`kubeadm upgrade apply v1.22.5`
`kubelet --version`
`kubectl --version`
`apt-mark unhold kubectl kubelet`
`apt-get install kubelet-1.22.5-00 kubectl-1.22.5-0`
`service kubelet restart`
`service kubelet status`
`kubeadm upgrade plan`
`kubectl get nodes`
`kubectl uncordon cks-controlplane`


upgrade worker node

Do this on control node
`kubectl drain worker-node`
`kubectl drain worker-node --ignore-daemonsets`


Do this on worker node
`apt-get update`
`apt-mark hold kubectl kubelet`
`apt-mark unhold kubeadm`
`apt-get install kubadm-1.22.5-00`
`kubeadm upgrade node`
`apt-mark unhold kubeadmn`
`apt-mark unhold kubelet kubectl`
`apt-get install kubelet-1.22.5-00 kubectl-1.22.55-00`
`service restart kubelet`
`service restart kubectl`

On control plane
`kubectl get node`
`kubectl uncordon worker-node`


## Micro-service vulnerabilities - Manage Kubernetes Secrets

Two ways in which secrets can be added to pod
- Using volume mount
- Using env variable


Using secret using volume
To add secret using volume mount, you need to first create a volume ( not this volume is ephemeral), for persistent use persistent volume

Once volume is created, add secret by referencing it by name ( same level as container)
```
volumes:
- name: secretVol
  secret:
	  secretName: secret
```


Now use that volume `secretVol` into pod using `volumeMounts` ( inside container)

```
volumeMounts:
- name: secretVol <= name of the volume
  mountPath: "/etc/foo" <= path
  readOnly: true
```

Using secret using env variable ( inside cointainer )
```
env:
	- name: PASS
	  valueFrom:
		  secretKeyRef:
			  name: secret1 <= secret name
			  key: pass
```



See secret from ETCD

`ctictl ps`
`crictl inspect containerID`


Check ETCD health
`ETCDCTL_API=3 etcdctl endpoint health`



Task
Create a secret and attach secret to pod using volume mount and using env variable


View secret and decode them
`kubectl get secrets -n namespace`
	`kubectl describe secret SECRET_NAME -n namespace`
`kubectl get secrets SECRET_NAME -ojsonpath="{.data.data}"` | base64 -d


Encryption Configuration for encrypting secrets at rest in ETCD

```javascript
mkdir -p /etc/kubernetes/etcd
echo -n this-is-very-sec | base64
```

```
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
  - resources:
    - secrets
    providers:
	    - aesgcm:
	        keys:
	        - name: key1
	          secret: dGhpcy1pcy12ZXJ5LXNlYw==
	    - identity: {}
```



```yaml
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
  - resources:
    - secrets
    providers:
	    - identity: {}
	    - aesgcm:
	        keys:
	        - name: key1
	          secret: dGhpcy1pcy12ZXJ5LXNlYw==
```


Encrypt all secrets ( replace )

`kubectl get secrets --all-namespaces -o json | kubectl replace -f -`

Read secrets from ETCD

`ETCDCTL_API=3 etcdctl --cert PATHTOCERT --key PATHTOKEY --cacert PATHTOCACERT get /registry/secrets/default/secret1`



## Micro-service vulnerabilities - Container Runtime Sandboxes

Linux kernel dirty cow: Kernel exploit

OCI ( Open Container Initiative )
- Maintain specification for runtime, image, distribution
- Runtime
	- `runc` - container runtime which implements OCI specification for a container runtime
- Docker 
	- Docker uses -> `moby/mody` uses -> `containerd` uses -> `oci/runc` creates  container using ( implements `oci/runtime` spec ) -> `oci/runc/libcontainer`
- `dockershim`
	- What is a shim ?
- `cri-o`

`kubelet --container-runtime {string} --container-runtime-endpoint {}
`
- `Kubelet` can only run with one container runtime at any given time
- Kata containers 
	- Here containers are VM
	- Each kata container has its own kernel
	- Strong separation layer
	- Rune every container in its own private VM ( Hypervisor based)
	- QEMU as default
		- Needs virtualization like nested virtualization in cloud

gVisor
- user space kernel for container ( kernel which runs in user space)
- not hypervisor / vm based
- simulates kernel syscalls with limited functionality
- Runs in userspace separated from linux kernel
- runtime called `runsc`
-

Task
- Create and use RuntimeClaess for runtime `runsc` ( gvisor)
- Install gvisor /runsc with conatinerd


Runtimeclass

```yaml
apiVersion: node.k8s.io/v1
kind: RuntimeClass
metadata:
  name: gvisor
handler: runsc
```


pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sec
spec:
  runtimeClassName: gvisor
  containers:
    - image: nginx:1.21.5-alpine
      name: sec
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```

Verify
```
kubectl exec sec -- dmesg | grep -i gvisor
```


## Micro-service vulnerabilities: OS Level Security Domains

- Security Contexts (pod and container level)
	- runAsUser
	- runAsGroup
	- fsGroup
- force container to run as non-root
	- `runAsNonRoot: true`
- Privileged containers
	- Privileged means container user 0 (container root user ) is directly mapped to host user 0 ( host root user )
	- `docker run --privileged`
	- in k8s
		- `privileged: true` (on container level security context)
- Enable privileged and test using sysctl
- privileged escalation
	- `AllowPrivlegeEscalation` controls whether a process can gain more privileges than its parent process



## Microservice vulnerabilities: mTLS
mTLS communication and how does it work

- Mutual TLS
- bilateral authentication
- two parties authenticate
- sidecar container
- Create proxy sidebar with capability
	- Security Context
- Istio sidecar injection model

## Open Policy Agent (OPA)

- OPA is an policy engine that enables unified context aware policy enforcement across the entire stack
- using Rego ( similar to GO )
- Works with JSON and YAML
- Gatekeeper
	- Uses OPA along with CRD to use OPA in k8s
	- Uses `ConstraiintTemplate`
		- template can be used for multiple purpose
	- `contstraint template` and `constraints`
	- After we create constraint template, we can use the name of the template as a custom CRD in k8s

## Supply chain security: Image footprint

Dockerfile hardening
1. Use specific package version
2. Do not run as root `RUN addgroup -S appgroup && adduser -S appuser -G appgroup -h /home/appuser`
	`USER appuser`
1. Make filesystem readonly `RUN chmod a-w /etc`
2. Remove shell access (do this at last) `RUN rm -rf /bin/*`


TASK
- Build and run docker container 
- Run docker container as a seperate user
- Image hardening
	- Use specific image at top
	- Pass secrets via ENV argument ````bash
podman run -e TOKEN=2e064aad-3a90-4cde-ad86-16fad1f8943e app
- Prevent Bash exec `RUN rm /usr/bin/bash`


## Sipply chain security: Static Chain Security. ( Kubesec , OPA )
Static analysis
- Always define request and limit
- pod should never use default service account

Kubesec using docker
- scan using 

OPA - Conftest
- unit test k8s configuration
- As part of OPA
- Uses rego language


## Supply chain security: Image vulnerability scanning ( Clair and Trivy)

Task
- Use trivy to scan images for known vulnerabilities
- `k -n applications get pod -oyaml | grep image:`
```
# find images
k -n applications get pod -oyaml | grep image:

# scan first deployment
trivy image nginx:1.19.1-alpine-perl | grep CVE-2021-28831
trivy image nginx:1.19.1-alpine-perl | grep CVE-2016-9841

# scan second deployment
trivy image nginx:1.20.2-alpine | grep CVE-2021-28831
trivy image nginx:1.20.2-alpine | grep CVE-2016-9841

# hit on the first one, so we scale down
k -n applications scale deploy web1 --replicas 0
```

## Supply Chain Security: Secure Supply Chain

- List all image registries used in the whole cluster
```
kubectl get pod -A -oyaml | grep "image:" | grep -v "f:"
```

- Use image digest for kube-api server


`vi /etc/kubernetes/policywebhook/admission_config.json`

```
{
   "apiVersion": "apiserver.config.k8s.io/v1",
   "kind": "AdmissionConfiguration",
   "plugins": [
      {
         "name": "ImagePolicyWebhook",
         "configuration": {
            "imagePolicy": {
               "kubeConfigFile": "/etc/kubernetes/policywebhook/kubeconf",
               "allowTTL": 100,
               "denyTTL": 50,
               "retryBackoff": 500,
               "defaultAllow": false
            }
         }
      }
   ]
}
```


`vi /etc/kubernetes/policywebhook/kubeconf`


```
clusters:
- cluster:
    certificate-authority: /etc/kubernetes/policywebhook/external-cert.pem  # CA for verifying the remote service.
    server: https://localhost:1234                  # URL of remote service to query. Must use 'https'.
  name: image-checker
```


## Runtime security: Strace / proc, falco, syscall
Strace
- `strace -cw ls /`

list syscalls
`strace -p PID -f -cw`



find open files
`cd /proc/PID`
`ls -la exe`
`cd fd`

grep 10 -20 lines
`grep -A20 -B20`

Create apache pod with a secret as environment variable
`kubectl run apache --image=httpd -oyaml --dry-run=client > a.yaml`

`pstree -p`


Falco
- runtime security 
- ACCESS
	- Deep kernel tracing built on the linux kerner
- ASSERT
	- Describe security rules against a system
	- detect unwanted behaviour
- ACTION
	- automated respond to a security variation

- `/etc/falco/rules`

```
cd /etc/falco
grep -r "Package management process launched" .
cp falco_rules.yaml falco_rules.yaml_ori

```

``%evt.time,%container.id,%container.name,%user.name`

`cat /opt/course/2/falco.log.dirty | cut -d" " -f 9 > /opt/course/2/falco.log`

Falco rule change (Important)
- `service falco status`
- `service falco stop`
- `grep -r "rule test" .`
- `vi /etc/falco/falco_rules.yaml`
- `vi /etc/falco/falo_rules.local.yaml` ( Override here not on original file )


Use `strace` to see what kind of syscalls the `kube-apiserver` process performs.

ps aux | grep kube-apiserver

```yaml
strace -p 19890 -f -cw # use your PID
```


## Runtime security:  Container immutability
Ways to make containers immutable
- remove bash shell
- make filesystem RO
- run as user and non root

Use startup probe to remove touch and bash from containers
- Similar to liveliness but `StartupProbe`

Create pod security context to make filesystem RO ( some dir is writable)
- `readOnlyRootFilesystem: true`

Using `emptyDir` volume
- `emptyDir`
- 

## Runtime security: Auditing ( Imp )

https://kubernetes.io/docs/tasks/debug/debug-cluster/audit/


Configure api-server to store audit logs in JSON format
- configure logBackend
- create folder
- Make folder available to container using volumeMounts / mountPath
- Add volumes

Restrict logged data with audit policy
- 


```yaml
# add new Volumes
volumes:
  - name: audit-policy
    hostPath:
      path: /etc/kubernetes/audit-policy/policy.yaml
      type: File
  - name: audit-logs
    hostPath:
      path: /etc/kubernetes/audit-logs
      type: DirectoryOrCreate
```

```yaml
# add new VolumeMounts
volumeMounts:
  - mountPath: /etc/kubernetes/audit-policy/policy.yaml
    name: audit-policy
    readOnly: true
  - mountPath: /etc/kubernetes/audit-logs
    name: audit-logs
    readOnly: false
```


```yaml
# enable Audit Logs
spec:
  containers:
  - command:
    - kube-apiserver
    - --audit-policy-file=/etc/kubernetes/audit-policy/policy.yaml
    - --audit-log-path=/etc/kubernetes/audit-logs/audit.log
    - --audit-log-maxsize=7
    - --audit-log-maxbackup=2
```


## System hardening: Kernel hardening tools
Seccomp
- app armor
- SecComp

Linux kernel isolation
- Namespace

AppArmor
- create profile in app armor
- profile mode
	- enforce: Process cannot escape the configuration
	- complain: process can escape but it will be logged
	- unconfined: process can escape

```
aa-status - show profies

aa-genprof - generate new profile

aa-complain - 

aa-enforce

aa-logprof
```


Setup AppArmor profile for curl

```
apt-get install apparmor-utils
```


1. `aa-gerprof curl`
2. `aa-status`
3. `cd /etc/apparmor.d`
4. `aa-logprof`


Nginx docker container uses AppArmor profile
```
docker run --security-opt apparmor=docker-default nginx
```

Kubernetes
- container runtime needs to support AppArmor
- AppArmor needs to be installed in every node
- AppArmor profile needs to be available on every node
- AppArmor profiles are specified per container
	- Done using annotations


Create Pod which uses AppArmor profile
- add in annotation

Seccomp ( security computing mode )
- Used to restrict what syscall a process can make 

`seccomp-bpf`
- seccomp + BPF filters

`docker ruin --security-opt seccomp=default.json nginx`

Kubernetes pod with seccomp profile
- 

Verify if AppArmor profile exists
```
ssh node01 aa-status | grep 
```


Change profiel name and install
- change profile 
- parse `apparmor_parses /root/profile`
- `aa-status | grep profile name`
- `scp /root/profile node01:/root`
- `ssh node01`
- `apparmor_parses /root/profile`
- `aa-status | grep `


## System hardening: Reduce surface attack


Open ports
`netstat -pltn` or `ss`

`lsof -i :22`

List service
`ps aux`

Services
`systemctl`


Install and investigate services
`apt-get update`

`apt-get install vsftpd samba`

Find and disable the application that is listening on port 21
`netstat -pltn | grep 21`

`lsof -i :21`

`systemctl list-units --type service | grep ftp`

`systemctl status vsftpd`

