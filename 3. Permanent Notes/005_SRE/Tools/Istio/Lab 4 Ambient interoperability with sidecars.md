
All the Pods don't need to use the new Ambient mode.

You can have some Pods using sidecars while others are using Ambient.

Let's create a new namespace called `httpbin`:

`kubectl create namespace httpbin`

To use sidecars in this namespace, you need to label it accordingly:

`kubectl label namespace httpbin istio-injection=enabled`

Then, you can deploy the httpbin application:

`kubectl apply -n httpbin -f - <<EOF apiVersion: v1 kind: ServiceAccount metadata:   name: httpbin --- apiVersion: v1 kind: Service metadata:   name: httpbin  labels:    app: httpbin    service: httpbin spec:   ports:  - name: http    port: 8000    targetPort: 80  selector:    app: httpbin --- apiVersion: apps/v1 kind: Deployment metadata:   name: httpbin spec:   replicas: 1  selector:    matchLabels:      app: httpbin      version: v1  template:    metadata:      labels:        app: httpbin        version: v1    spec:      serviceAccountName: httpbin      containers:      - image: docker.io/kennethreitz/httpbin        imagePullPolicy: IfNotPresent        name: httpbin        ports:        - containerPort: 80 EOF`

Finally, we can send a request from the `sleep` Pod (ambient mode) to the `httpbin` Pod (sidecar):

`kubectl exec deploy/sleep -- curl http://httpbin.httpbin.svc.cluster.local:8000/get`

You should get something like this:

`{   "args": {},   "headers": {     "Accept": "*/*",     "Host": "httpbin.httpbin.svc.cluster.local:8000",     "User-Agent": "curl/7.69.1",     "X-B3-Sampled": "0",     "X-B3-Spanid": "6077510bb5518fe9",     "X-B3-Traceid": "000e61ea1432b6bb6077510bb5518fe9",     "X-Forwarded-Client-Cert": "By=spiffe://cluster.local/ns/httpbin/sa/httpbin;Hash=38cd6dbe6ad7695f7d76ed110e6acdc90e842397b7c968e0ecee3e67f96634e1;Subject=\"\";URI=spiffe://cluster.local/ns/default/sa/sleep"   },   "origin": "127.0.0.1",   "url": "https://httpbin.httpbin.svc.cluster.local:8000/get" }`

You can see that the `httpbin` application has received the request with the `X-Forwarded-Client-Cert` indicating that the request was sent by a Pod with the identity corresponding to the `sleep` service account.
Github actions

```
name: Trivy Scan

on:

# Run weekly

schedule:

- cron: '0 12 * * 1'

# Allow manual runs

workflow_dispatch:

jobs:

trivy-scan:

strategy:

matrix:

branch:

- main

- release-1.24

- release-1.23

- release-1.22

runs-on: ubuntu-latest

steps:

- uses: actions/checkout@v3

with:

ref: ${{ matrix.branch }}

- uses: aquasecurity/trivy-action@0.9.2

with:

scanners: vuln

scan-type: 'fs'

format: 'sarif'

output: 'trivy-results.sarif'

ignore-unfixed: true

severity: 'HIGH,CRITICAL'

- uses: github/codeql-action/upload-sarif@v2

with:

sarif_file: 'trivy-results.sarif'
```