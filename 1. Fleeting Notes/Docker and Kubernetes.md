
1. Containers vs VMs
    
2. Understanding process isolation
    
3. Is Docker the only one?
    
4. Docker client-server architecture
    
5. Running containers
    
6. Building Docker images
    
7. Mounting volumes
    
8. Exposing ports
    
9. Managing containers lifecycle
    
10. Injecting environment variables
    
11. Debugging running containers

1. Managing containers at scale
    
2. The battle of container orchestrators
    
3. Visualising the data centre as a single VM
    
4. The best Tetris player
    
5. Exploring an API over your infrastructure
    
6. What are Pods, Services, and Ingresses?
    
7. Creating a local cluster with minikube
    
8. Creating a Deployment
    
9. Exposing Deployments
    
10. What is a Pod?
    
11. Scaling applications
    
12. Testing resiliency

1. Monitoring for uptime
    
2. Liveness probe
    
3. Readiness probe
    
4. Executing zero downtime deployments
    
5. Using labels and selectors
    
6. Releasing features with canary deployments
    
7. Releasing features with blue-green deployments
    
8. Preparing for rollbacks

1. Single and multi-node clusters
    
2. Examining the control plane
    
3. Persisting changes in etcd
    
4. Syncing changes with RAFT
    
5. Event-based architecture
    
6. Understanding the kubelet
    
7. Verifying "no single point of failure"
    
8. Setting up a multi-master cluster
    
9. Investigating multi-master setup in EKS
    
10. Exploring multi-master setup in Monzo
    
11. Creating a 3 node cluster with kubeadm
    
12. Installing an overlay network
    
13. Installing an Ingress controller
    
14. Exploring the API without kubectl
    
15. Taking down the cluster one node at the time

1. Creating reusable templates
    
2. Helm's templating engine
    
3. Understanding the Helm architecture
    
4. Templating resources with Go and Sprig
    
5. Managing releases with Helm
    
6. Writing helper functions
    
7. Reverting changes with rollbacks
    
8. Depending on other charts
    
9. Storing reusable templates in repositories

1. How the Scheduler works in Kubernetes
    
2. Filters and predicates
    
3. nodeSelector
    
4. Node affinity
    
5. Pod affinity/anti-affinity
    
6. Taints and tolerations
    
7. Scheduler plugins
    
8. Custom scheduler

1. Network routing in Linux
    
2. Understanding network requirements
    
3. Exploring the Endpoints
    
4. Balancing in-cluster traffic
    
5. Routing traffic with kube-proxy
    
6. CRI, CNI, CSI: interfaces for the kubelet
    
7. Choosing between latency and load balancing
    
8. Pros and cons of the 4 types of Services
    
9. Discovering Services
    
10. Routing traffic with an Ingress controller
    
11. End-to-end traffic journey

Dive into the specifics of network interfaces, IP addresses and network topologies in this course about advanced Kubernetes networking. Learn how to build your Kubernetes network and how the Container Network Interface (CNI) works. And while you're at it why not try making your very own Container Network Interface (CNI)?

1. Exploring the Node network
    
2. Cluster network

How does Kubernetes store state? Can you host databases in your cluster? Can you extract configurations and share them with different deployments? How do you make sure that your storage layer is replicated and persisted even if a node becomes unavailable? In this course, you will learn how to deploy a database with durable persistence.

1. Managing configurations
    
2. Managing secrets
    
3. Using Kubernetes Volumes
    
4. Creating Persistent Volumes
    
5. Creating Persistent Volume Claims
    
6. Provisioning volumes dynamically
    
7. Managing stateful applications
    
8. Creating volumes on bare metal
    
9. Deploying a single database with persistence
    
10. Deploying a clustered database with persistence
    
11. Designing storage that can span multiple nodes

After deploying your app to production, the received traffic may change in unpredictable ways. How do you keep your app responsive at all times? You can adapt the number of replicas. But is it feasible to do this manually, or are there better ways? In this course, you will learn how to autoscale an application based on an application-specific custom metric.

1. Introduction to autoscaling
    
2. The Horizontal Pod Autoscaler
    
3. The Kubernetes metrics registry
    
4. Exposing metrics from your apps
    
5. Installing and configuring Prometheus
    
6. Understanding custom and external metrics adapters
    
7. Tuning the Horizontal Pod Autoscaler

Wear your black hat and try to break the cluster. Study mitigation and countermeasure to secure your cluster against malicious attacks.

1. Role-based access control
