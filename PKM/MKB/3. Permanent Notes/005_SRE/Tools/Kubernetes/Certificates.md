
### All You Need to Know About Certificates in Kubernetes
https://www.youtube.com/watch?v=gXz4cq3PKdg

### Kubernetes Components
https://kubernetes.io/docs/concepts/overview/components

### PKI certificates and requirements
https://kubernetes.io/docs/setup/best-practices/certificates


## Architecture
### CA ( Certificate Authority ) / PKI ( Public Key Infrastructure)
- CA signs the certificate
- CA is the trusted root for all certificates inside the cluster
- Used by components to validate each other
- APIServer cert, kubelet cert, scheduler cert

### PKI
- API server has a server cert
- Clients ( kubelet, controller manager ) uses client certificate to communicate with API server
- kubelet os also has server cert which api server
- ETCD has it own certificate authority as well 

### On control node

```
cd /etc/kubernetes/pki
```


### X.509 Client cert authentication
- strategy for authenticating request that preset a client certificate
- Mainly used by k8s components, but ca also be used for end user authentication
- User is obtained from CN field
- Groups are obtained for Organization field


### Kubelet client certificates
- each kubelet on cluster has its own identity
- Enable use of Node Authorizer and Node Restriction Admission plugin
- Limit k8s read and write access to resources that are related to the node itself and pods bound to the node

#### Certificate generation request
- client creates a certificate signing request ( CSR ) and send it to API server
- requesting user is stored as part of CSR 
- CSR remains in pending state, until approved by admin


### Get CSR

```
kubectl get csr [CSR_NAME] -o yaml
kubectl get csr [CSR_NAME] -o jsonpath='{.status.certificate}' | base64 -d
```


### Initial certificate bootstrapping
- kubelet creates a CSR using bootstrap token
- `CSRApprovingController` approves the CSR automatically
- `CSRSignerController` signs the CSR
- kubelet download the generated certificate and starts using it


### Kubelet cert rotation
- kubelet can request new client cert when one using is nearing expiration
- it can also rotate serving certificate 


### CA
CA is a company or organization or entity that acts to validate the identities of entities such as website, email, company and bind them to a cryptographic keys through the issuance of electronic documents such as digital certificates

### Digital certificates
- provides validation of authenticity
- provides encryption for secure communication
- provides integrity to check against alteration

### X.509
- It is a standard format for public key certificates
- https://datatracker.ietf.org/doc/html/rfc5280

### Certificate chain of trust
- It is the hierarchy of certificates used to verify the validity of certificate issuer
- Consists of
	- Trust anchor: root CA
	- At least one intermediate CA: 
	- End entity certificate: used to validate identity