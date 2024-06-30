# istio-install-guide

Drop Kubeadm and Setup Minikube v1.33 with k8s version v1.23.17
Install Minikube v1.33 with Kubernetes v1.23.17
Install Istio 1.11.8 via Istioctl

```sh
wget https://github.com/istio/istio/releases/download/1.11.8/istio-1.11.8-linux-amd64.tar.gz
tar -xvf istio-1.11.8-linux-amd64.tar.gz
cd istio-1.11.8/
./bin/istioctl install
kubectl apply -f n.yaml
kubectl label namespace default istio-injection=enabled
kubectl rollout restart deployment -n default
```


We can see now the sidecar gets injected successfully in the nginx pod and it uses istio-proxy 1.11.8

```sh
kubectl describe pod -n default nginx-8d545c96d-sllhn

Name:         nginx-8d545c96d-sllhn
Namespace:    default
Priority:     0
Node:         apxd-cluster-3/10.103.16.198
Start Time:   Fri, 03 May 2024 10:49:24 +0000
Labels:       app=nginx
              pod-template-hash=8d545c96d
              security.istio.io/tlsMode=istio
              service.istio.io/canonical-name=nginx
              service.istio.io/canonical-revision=latest
Annotations:  kubectl.kubernetes.io/default-container: nginx
              kubectl.kubernetes.io/default-logs-container: nginx
              prometheus.io/path: /stats/prometheus
              prometheus.io/port: 15020
              prometheus.io/scrape: true
              sidecar.istio.io/status:
                {"initContainers":["istio-init"],"containers":["istio-proxy"],"volumes":["istio-envoy","istio-data","istio-podinfo","istio-token","istiod-...
Status:       Running
IP:           10.244.0.9
IPs:
  IP:           10.244.0.9
Controlled By:  ReplicaSet/nginx-8d545c96d
Init Containers:
  istio-init:
    Container ID:  containerd://5b46900367429eba736e6ce43ed915dba8dd74e5f78403c6781a0e9f723f4d78
    Image:         docker.io/istio/proxyv2:1.11.8
    Image ID:      docker.io/istio/proxyv2@sha256:6734c59bab78320fcb2f38cc5da0d6c8a40e484a8eaac5fa6709fe1e4ddec25e
    Port:          <none>
    Host Port:     <none>
    Args:
      istio-iptables
      -p
      15001
      -z
      15006
      -u
      1337
      -m
      REDIRECT
      -i
      *
      -x
      
      -b
      *
      -d
      15090,15021,15020
    State:          Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Fri, 03 May 2024 10:49:25 +0000
      Finished:     Fri, 03 May 2024 10:49:25 +0000
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     2
      memory:  1Gi
    Requests:
      cpu:        100m
      memory:     128Mi
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-w5rd7 (ro)
Containers:
  nginx:
    Container ID:   containerd://f13be5cefb153e2bd2083f2e35c64e1bef9df3840d87969d65f4d2c2af8a6060
    Image:          nginx:latest
    Image ID:       docker.io/library/nginx@sha256:ed6d2c43c8fbcd3eaa44c9dab6d94cb346234476230dc1681227aa72d07181ee
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Fri, 03 May 2024 10:49:26 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-w5rd7 (ro)
  istio-proxy:
    Container ID:  containerd://62322c5fa1a3e7e2823f43676cbf994377f06e59bd55a7ff10ca44a76269c734
    Image:         docker.io/istio/proxyv2:1.11.8
    Image ID:      docker.io/istio/proxyv2@sha256:6734c59bab78320fcb2f38cc5da0d6c8a40e484a8eaac5fa6709fe1e4ddec25e
    Port:          15090/TCP
    Host Port:     0/TCP
    Args:
      proxy
      sidecar
      --domain
      $(POD_NAMESPACE).svc.cluster.local
      --proxyLogLevel=warning
      --proxyComponentLogLevel=misc:error
      --log_output_level=default:info
      --concurrency
      2
    State:          Running
      Started:      Fri, 03 May 2024 10:49:27 +0000
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     2
      memory:  1Gi
    Requests:
      cpu:      100m
      memory:   128Mi
    Readiness:  http-get http://:15021/healthz/ready delay=1s timeout=3s period=2s #success=1 #failure=30
    Environment:
      JWT_POLICY:                    third-party-jwt
      PILOT_CERT_PROVIDER:           istiod
      CA_ADDR:                       istiod.istio-system.svc:15012
      POD_NAME:                      nginx-8d545c96d-sllhn (v1:metadata.name)
      POD_NAMESPACE:                 default (v1:metadata.namespace)
      INSTANCE_IP:                    (v1:status.podIP)
      SERVICE_ACCOUNT:                (v1:spec.serviceAccountName)
      HOST_IP:                        (v1:status.hostIP)
      PROXY_CONFIG:                  {}
                                     
      ISTIO_META_POD_PORTS:          [
                                         {"containerPort":80,"protocol":"TCP"}
                                     ]
      ISTIO_META_APP_CONTAINERS:     nginx
      ISTIO_META_CLUSTER_ID:         Kubernetes
      ISTIO_META_INTERCEPTION_MODE:  REDIRECT
      ISTIO_META_WORKLOAD_NAME:      nginx
      ISTIO_META_OWNER:              kubernetes://apis/apps/v1/namespaces/default/deployments/nginx
      ISTIO_META_MESH_ID:            cluster.local
      TRUST_DOMAIN:                  cluster.local
    Mounts:
      /etc/istio/pod from istio-podinfo (rw)
      /etc/istio/proxy from istio-envoy (rw)
      /var/lib/istio/data from istio-data (rw)
      /var/run/secrets/istio from istiod-ca-cert (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-w5rd7 (ro)
      /var/run/secrets/tokens from istio-token (rw)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  istio-envoy:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     Memory
    SizeLimit:  <unset>
  istio-data:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     
    SizeLimit:  <unset>
  istio-podinfo:
    Type:  DownwardAPI (a volume populated by information about the pod)
    Items:
      metadata.labels -> labels
      metadata.annotations -> annotations
  istio-token:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  43200
  istiod-ca-cert:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      istio-ca-root-cert
    Optional:  false
  kube-api-access-w5rd7:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason            Age                   From               Message
  ----     ------            ----                  ----               -------
  Normal   Scheduled         5m53s                 default-scheduler  Successfully assigned default/nginx-8d545c96d-sllhn to apxd-cluster-3
  Normal   Pulled            5m52s                 kubelet            Container image "docker.io/istio/proxyv2:1.11.8" already present on machine
  Normal   Created           5m52s                 kubelet            Created container istio-init
  Normal   Started           5m52s                 kubelet            Started container istio-init
  Normal   Pulling           5m52s                 kubelet            Pulling image "nginx:latest"
  Normal   Pulled            5m51s                 kubelet            Successfully pulled image "nginx:latest" in 1.286084833s (1.286090988s including waiting)
  Normal   Created           5m51s                 kubelet            Created container nginx
  Normal   Started           5m51s                 kubelet            Started container nginx
  Normal   Pulled            5m51s                 kubelet            Container image "docker.io/istio/proxyv2:1.11.8" already present on machine
  Normal   Created           5m51s                 kubelet            Created container istio-proxy
  Normal   Started           5m50s                 kubelet            Started container istio-proxy
  Warning  DNSConfigForming  20s (x11 over 5m53s)  kubelet            Search Line limits were exceeded, some search paths have been omitted, the applied search line is: default.svc.cluster.local svc.cluster.local cluster.local hpc.amslabs.hpecorp.net us.cray.com americas.cray.com
```

Setting up istio via Helm for version 1.12.0 since 1.11.8 is not available in istio helm repo anymore
```sh
helm repo add istio https://istio-release.storage.googleapis.com/charts
helm repo update
kubectl create namespace istio-system
helm install istio-base istio/base -n istio-system --version 1.12.0
helm install istiod istio/istiod -n istio-system --wait --version 1.12.0
kubectl create namespace istio-ingress
helm install istio-ingress istio/gateway -n istio-ingress --wait --version 1.12.0
kubectl label namespace default istio-injection=enabled
kubectl apply -f n.yaml
```

We can see that the nginx pod has the sidecar container injected successfully

```sh
kubectl describe pod nginx-65478d76d4-tlbkq     

Name:         nginx-65478d76d4-tlbkq
Namespace:    default
Priority:     0
Node:         apxd-cluster-3/10.103.16.198
Start Time:   Fri, 03 May 2024 11:03:44 +0000
Labels:       app=nginx
              pod-template-hash=65478d76d4
              security.istio.io/tlsMode=istio
              service.istio.io/canonical-name=nginx
              service.istio.io/canonical-revision=latest
Annotations:  kubectl.kubernetes.io/default-container: nginx
              kubectl.kubernetes.io/default-logs-container: nginx
              kubectl.kubernetes.io/restartedAt: 2024-05-03T11:03:44Z
              prometheus.io/path: /stats/prometheus
              prometheus.io/port: 15020
              prometheus.io/scrape: true
              sidecar.istio.io/status:
                {"initContainers":["istio-init"],"containers":["istio-proxy"],"volumes":["istio-envoy","istio-data","istio-podinfo","istio-token","istiod-...
Status:       Running
IP:           10.244.0.11
IPs:
  IP:           10.244.0.11
Controlled By:  ReplicaSet/nginx-65478d76d4
Init Containers:
  istio-init:
    Container ID:  containerd://abb542d30c314647e34b2ff7b27d0a97508e8150e04d1ad33ebfb9d7d15f1f18
    Image:         docker.io/istio/proxyv2:1.13.9
    Image ID:      docker.io/istio/proxyv2@sha256:550acb62103710314db1bf0d1e7410d413e866ffef0769b1b6b6ec82bda9ba2b
    Port:          <none>
    Host Port:     <none>
    Args:
      istio-iptables
      -p
      15001
      -z
      15006
      -u
      1337
      -m
      REDIRECT
      -i
      *
      -x
      
      -b
      *
      -d
      15090,15021,15020
    State:          Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Fri, 03 May 2024 11:03:45 +0000
      Finished:     Fri, 03 May 2024 11:03:46 +0000
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     2
      memory:  1Gi
    Requests:
      cpu:        100m
      memory:     128Mi
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-67pl6 (ro)
Containers:
  nginx:
    Container ID:   containerd://f29d1a96aea3668dbf73ecb53ed5473e2b4da6f52d64d6ed470d4b8d5f2f908b
    Image:          nginx:latest
    Image ID:       docker.io/library/nginx@sha256:ed6d2c43c8fbcd3eaa44c9dab6d94cb346234476230dc1681227aa72d07181ee
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Fri, 03 May 2024 11:03:48 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-67pl6 (ro)
  istio-proxy:
    Container ID:  containerd://7d549626a66d5f6a82ad59470c24add93d2bee999cfaffc65cec72a79fea8fc1
    Image:         docker.io/istio/proxyv2:1.13.9
    Image ID:      docker.io/istio/proxyv2@sha256:550acb62103710314db1bf0d1e7410d413e866ffef0769b1b6b6ec82bda9ba2b
    Port:          15090/TCP
    Host Port:     0/TCP
    Args:
      proxy
      sidecar
      --domain
      $(POD_NAMESPACE).svc.cluster.local
      --proxyLogLevel=warning
      --proxyComponentLogLevel=misc:error
      --log_output_level=default:info
      --concurrency
      2
    State:          Running
      Started:      Fri, 03 May 2024 11:03:48 +0000
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     2
      memory:  1Gi
    Requests:
      cpu:      100m
      memory:   128Mi
    Readiness:  http-get http://:15021/healthz/ready delay=1s timeout=3s period=2s #success=1 #failure=30
    Environment:
      JWT_POLICY:                    third-party-jwt
      PILOT_CERT_PROVIDER:           istiod
      CA_ADDR:                       istiod.istio-system.svc:15012
      POD_NAME:                      nginx-65478d76d4-tlbkq (v1:metadata.name)
      POD_NAMESPACE:                 default (v1:metadata.namespace)
      INSTANCE_IP:                    (v1:status.podIP)
      SERVICE_ACCOUNT:                (v1:spec.serviceAccountName)
      HOST_IP:                        (v1:status.hostIP)
      PROXY_CONFIG:                  {}
                                     
      ISTIO_META_POD_PORTS:          [
                                         {"containerPort":80,"protocol":"TCP"}
                                     ]
      ISTIO_META_APP_CONTAINERS:     nginx
      ISTIO_META_CLUSTER_ID:         Kubernetes
      ISTIO_META_INTERCEPTION_MODE:  REDIRECT
      ISTIO_META_WORKLOAD_NAME:      nginx
      ISTIO_META_OWNER:              kubernetes://apis/apps/v1/namespaces/default/deployments/nginx
      ISTIO_META_MESH_ID:            cluster.local
      TRUST_DOMAIN:                  cluster.local
    Mounts:
      /etc/istio/pod from istio-podinfo (rw)
      /etc/istio/proxy from istio-envoy (rw)
      /var/lib/istio/data from istio-data (rw)
      /var/run/secrets/istio from istiod-ca-cert (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-67pl6 (ro)
      /var/run/secrets/tokens from istio-token (rw)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  istio-envoy:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     Memory
    SizeLimit:  <unset>
  istio-data:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     
    SizeLimit:  <unset>
  istio-podinfo:
    Type:  DownwardAPI (a volume populated by information about the pod)
    Items:
      metadata.labels -> labels
      metadata.annotations -> annotations
  istio-token:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  43200
  istiod-ca-cert:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      istio-ca-root-cert
    Optional:  false
  kube-api-access-67pl6:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason            Age                   From               Message
  ----     ------            ----                  ----               -------
  Normal   Scheduled         38m                   default-scheduler  Successfully assigned default/nginx-65478d76d4-tlbkq to apxd-cluster-3
  Normal   Pulled            38m                   kubelet            Container image "docker.io/istio/proxyv2:1.13.9" already present on machine
  Normal   Created           38m                   kubelet            Created container istio-init
  Normal   Started           38m                   kubelet            Started container istio-init
  Normal   Pulling           38m                   kubelet            Pulling image "nginx:latest"
  Normal   Pulled            38m                   kubelet            Successfully pulled image "nginx:latest" in 1.191674874s (1.191681574s including waiting)
  Normal   Created           38m                   kubelet            Created container nginx
  Normal   Started           38m                   kubelet            Started container nginx
  Normal   Pulled            38m                   kubelet            Container image "docker.io/istio/proxyv2:1.13.9" already present on machine
  Normal   Created           38m                   kubelet            Created container istio-proxy
  Normal   Started           38m                   kubelet            Started container istio-proxy
  Warning  DNSConfigForming  3m13s (x33 over 38m)  kubelet            Search Line limits were exceeded, some search paths have been omitted, the applied search line is: default.svc.cluster.local svc.cluster.local cluster.local hpc.amslabs.hpecorp.net us.cray.com americas.cray.com
```

So now we can move on to upgrading the istio components to the required version
