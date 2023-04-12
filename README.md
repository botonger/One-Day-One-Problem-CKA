# One-Day-One-Problem-CKA 


#### 🎃 0402

```bash
kubectl create serviceaccount pvviewer

kubectl create clusterrolebinding pvviewer-role-binding \
  --clusterrole=pvviewer-role \
  --serviceaccount=pvviewer
  
kubectl run pvviewer --image=redis --dry-run=client -o yaml
```

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: pvviewer
  namespace: default
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: pvviewer-role
rules:
  - verbs:
      - list
    resources:
      - persistentvolumes
    apiGroups:
      - v1
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: pvviewer-role-binding
subjects:
  - kind: ServiceAccount
    name: pvviewer
roleRef:
  kind: ClusterRole
  apiGroup: rbac.authorization.k8s.io
  name: pvviewer-role
---
apiVersion: v1
kind: Pod
metadata:
  name: pvviewer
  namespace: default
spec:
  serviceAccountName: pvviewer
  containers:
    - name: redis
```

#### 🤖 0403

```bash
kubectl create deployment nginx-deploy --image nginx:1.16 --dry-run=client --replicas=1 -o yaml

#https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/
kubectl set image deployment/nginx-deploy nginx=nginx:1.17 --record
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-deploy
  name: nginx-deploy
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-deploy
  strategy: {}
  template:
    metadata:
      labels:
        app: nginx-deploy
    spec:
      containers:
        - image: nginx:1.16
          name: nginx
          resources: {}
```

#### 🤖 0404

```bash
#gcp-specific
export CLUSTER_NAME=cluster

echo $(gcloud container clusters describe $CLUSTER_NAME --format='value(privateClusterConfig.privateEndpoint)') \
$(kubectl get node -o jsonpath='{.items[*].status.addresses[?(@.type=="InternalIP")].address}') \
> /root/CKA/node_ips
```

#### 😹 0406

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: ingress-to-nptest
spec:
  podSelector:
    matchLabels:
      app: np-test-1
  policyTypes:
  - Ingress
  ingress:
    - ports:
        - port: 80
          protocol: TCP
```

#### ✋ 0407

```bash
kubectl get pods -A -o jsonpath='{range .items[*]}{.metadata.namespace} {.metadata.name}{"\n"}{end}'

```

#### 👨‍🎨 0408

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi
spec:
  containers:
    - name: nginx
      image: nginx:1.21.6
      volumeMounts:
        - mountPath: /var/log/nginx
          name: logs
    - name: streaming
      image: busybox1.35.0
      args:
        - 'bin/sh'
        - '-c'
        - 'tail -n+1 -f /var/log/nginx/access.log'
      volumeMounts:
        - mountPath: /var/log/nginx
          name: logs
  volumes:
    - name: logs
      emptyDir: {}
```

#### 👨‍ 0410

```bash
kubectl run nginx --image=nginx --env=var1=val1
kubectl exec -it nginx -- env
```

#### 🙋‍♀️ 0411

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: stress-1
spec:
  containers:
    - image: polinux/stress:1.0.4
      name: stress-1
      args:
        - "/bin/sh"
        - "-c"
        - "stress --vm 1 --vm-bytes $(shuf -i 20-200 -n 1)M --vm-hang 1"
      resources:
        limits:
          memory: 250Mi
        requests:
          memory: 250Mi
```

```bash
kubectl top pods
echo "" > max.memory.txt
```

#### 🧵 0412

```bash
kubectl run my-pod --image=busybox -o yaml --dry-run=client -- /bin/sh -c 'echo hello; sleep 3600'
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: my-pod
  name: my-pod
spec:
  containers:
    - command:
        - /bin/sh
        - -c
        - echo hello; sleep 3600
      image: busybox
      name: my-con1
    - command:
        - /bin/sh
        - -c
        - echo hello; sleep 3600
      image: busybox
      name: my-con2
```

```bash
kubectl exec -it my-pod -c my-con2 -- ls
```
