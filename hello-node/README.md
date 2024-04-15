# Install MicroK8s 
MicroK8s will install a minimal, lightweight Kubernetes you can run and use on practically any machine. It can be installed with a snap:
1. Install MicroK8s
   ```bash
   sudo snap install microk8s --classic --channel=1.29
   sudo usermod -a -G microk8s $USER
   sudo mkdir -p ~/.kube
   sudo chown -f -R $USER ~/.kube
   microk8s enable dns
   microk8s enable hostpath-storage
   microk8s stop
   microk8s start
   ```
# How to Run?

2. Check the status.
  ```bash
  david@lmde:~$ microk8s status --wait-ready
microk8s is running
high-availability: no
  datastore master nodes: 127.0.0.1:19001
  datastore standby nodes: none
addons:
  enabled:
    argocd               # (community) Argo CD is a declarative continuous deployment for Kubernetes.
    dashboard-ingress    # (community) Ingress definition for Kubernetes dashboard
    fluentd              # (community) Elasticsearch-Fluentd-Kibana logging and monitoring
    gopaddle             # (community) DevSecOps and Multi-Cloud Kubernetes Platform
    istio                # (community) Core Istio service mesh services
    openfaas             # (community) OpenFaaS serverless framework
    cert-manager         # (core) Cloud native certificate management
    community            # (core) The community addons repository
    dashboard            # (core) The Kubernetes dashboard
    dns                  # (core) CoreDNS
    ha-cluster           # (core) Configure high availability on the current node
    helm                 # (core) Helm - the package manager for Kubernetes
    helm3                # (core) Helm 3 - the package manager for Kubernetes
    hostpath-storage     # (core) Storage class; allocates storage from host directory
    ingress              # (core) Ingress controller for external access
    metrics-server       # (core) K8s Metrics Server for API access to service metrics
    registry             # (core) Private image registry exposed on localhost:32000
    storage              # (core) Alias to hostpath-storage add-on, deprecated
  disabled:
    cilium               # (community) SDN, fast with full network policy
    easyhaproxy          # (community) EasyHAProxy can configure HAProxy automatically based on ingress labels
    inaccel              # (community) Simplifying FPGA management in Kubernetes
    jaeger               # (community) Kubernetes Jaeger operator with its simple config
    kata                 # (community) Kata Containers is a secure runtime with lightweight VMS
    keda                 # (community) Kubernetes-based Event Driven Autoscaling
    knative              # (community) Knative Serverless and Event Driven Applications
    kubearmor            # (community) Cloud-native runtime security enforcement system for k8s
    kwasm                # (community) WebAssembly support for WasmEdge (Docker Wasm) and Spin (Azure AKS WASI)
    linkerd              # (community) Linkerd is a service mesh for Kubernetes and other frameworks
    microcks             # (community) Open source Kubernetes Native tool for API Mocking and Testing
    multus               # (community) Multus CNI enables attaching multiple network interfaces to pods
    nfs                  # (community) NFS Server Provisioner
    ondat                # (community) Ondat is a software-defined, cloud native storage platform for Kubernetes.
    openebs              # (community) OpenEBS is the open-source storage solution for Kubernetes
    osm-edge             # (community) osm-edge is a lightweight SMI compatible service mesh for the edge-computing.
    parking              # (community) Static webserver to park a domain. Works with EasyHAProxy.
    portainer            # (community) Portainer UI for your Kubernetes cluster
    shifu                # (community) Kubernetes native IoT software development framework.
    sosivio              # (community) Kubernetes Predictive Troubleshooting, Observability, and Resource Optimization
    traefik              # (community) traefik Ingress controller
    trivy                # (community) Kubernetes-native security scanner
    cis-hardening        # (core) Apply CIS K8s hardening
    gpu                  # (core) Automatic enablement of Nvidia CUDA
    host-access          # (core) Allow Pods connecting to Host services smoothly
    kube-ovn             # (core) An advanced network fabric for Kubernetes
    mayastor             # (core) OpenEBS MayaStor
    metallb              # (core) Loadbalancer for your Kubernetes cluster
    minio                # (core) MinIO object storage
    observability        # (core) A lightweight observability stack for logs, traces and metrics
    prometheus           # (core) Prometheus operator for monitoring and logging
    rbac                 # (core) Role-Based Access Control for authorisation
    rook-ceph            # (core) Distributed Ceph storage using Rook
  ```

3. Deployment.
  ```bash
  kubectl create deployment hello-node --image=registry.k8s.io/e2e-test-images/agnhost:2.39 -- /agnhost netexec --http-port=8080
  ```

4. Check the status.
  ```bash
  david@lmde:~$ kubectl get deployment
  NAME         READY   UP-TO-DATE   AVAILABLE   AGE
  hello-node   1/1     1            1           2m3s
  ```

5. Services.
  ```bash
  kubectl expose deployment hello-node --type=LoadBalancer --port=8080
  david@lmde:~$ kubectl get services
  NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
  kubernetes   ClusterIP      10.152.183.1     <none>        443/TCP          122d
  hello-node   LoadBalancer   10.152.183.227   <pending>     8080:32315/TCP   89s
  ```

6. Ingress.
  ```bash
  david@lmde:~$ nano hello-node_ingress.yaml
  apiVersion: networking.k8s.io/v1
  kind: Ingress
   metadata:
     name: hello-node-ingress
   spec:
     rules:
       - http:
           paths:
             - path: /hello-node/
               pathType: Prefix
               backend:
                 service:
                   name: hello-node
                   port:
                     number: 8080

   kubectl create -f hello-node_ingress.yaml

   david@lmde:~$ kubectl apply -f hello-node_ingress.yaml
   ingress.networking.k8s.io/hello-node-ingress configured

   david@lmde:~$ kubectl get ingress
   NAME                 CLASS    HOSTS   ADDRESS     PORTS   AGE
   hello-node-ingress   public   *       127.0.0.1   80      40m
   david@lmde:~$ curl http://192.168.1.24/hello-node/
   NOW: 2024-04-1curl http://192.168.1.24/hello-node/
   NOW: 2024-04-12 18:01:49.053909239 +0000 UTC m=+7042.275608842david
  ```
