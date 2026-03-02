# Kubernetes + OpenShift Interview Coding Playbook

> **Your one-stop guide to WRITING K8s + OpenShift resources under interview pressure.**

---

## Table of Contents

- [0. How to Use This File](#0-how-to-use-this-file-in-interview-prep)
- [1. Core YAML Rules](#1-core-yaml-rules-you-must-memorize)
- [2. Kubectl Essentials](#2-kubectl-essentials)
- [3. Kubernetes Resources](#3-kubernetes-resources)
  - [A. Namespace](#a-namespace)
  - [B. Pod](#b-pod)
  - [C. Deployment](#c-deployment)
  - [D. ReplicaSet](#d-replicaset)
  - [E. Service](#e-service)
  - [F. ConfigMap](#f-configmap)
  - [G. Secret](#g-secret)
  - [H. Ingress](#h-ingress)
  - [I. ServiceAccount](#i-serviceaccount)
  - [J. RBAC: Role + RoleBinding](#j-rbac-role--rolebinding)
  - [K. Resource Requests/Limits](#k-resource-requestslimits)
  - [L. Liveness/Readiness Probes](#l-livenessreadiness-probes)
  - [M. Volumes](#m-volumes)
  - [N. PVC + PV](#n-pvc--pv)
  - [O. StatefulSet](#o-statefulset)
  - [P. DaemonSet](#p-daemonset)
  - [Q. Job + CronJob](#q-job--cronjob)
  - [R. HPA](#r-hpa)
  - [S. NetworkPolicy](#s-networkpolicy)
  - [T. Helm Basics](#t-helm-basics)
- [4. OpenShift Resources + oc CLI](#4-openshift-resources--oc-cli)
  - [A. Project](#a-project)
  - [B. DeploymentConfig vs Deployment](#b-deploymentconfig-vs-deployment)
  - [C. Route](#c-route)
  - [D. BuildConfig + ImageStream](#d-buildconfig--imagestream)
  - [E. SecurityContextConstraints](#e-securitycontextconstraints)
  - [F. oc Commands](#f-oc-commands)
- [5. Fast Copy Templates](#5-fast-copy-templates-section)
- [6. Debugging Playbook](#6-debugging-playbook)
- [7. Interview Drills](#7-interview-drills)
- [8. 7-Day Practice Plan](#8-7-day-practice-plan)

---

## 0. How to Use This File in Interview Prep

**Daily routine (10 minutes):**

| Step | Time | Action |
|------|------|--------|
| 1 | 2 min | Pick ONE resource from Section 3 |
| 2 | 3 min | Write the minimal YAML from memory |
| 3 | 2 min | Compare with template, fix mistakes |
| 4 | 2 min | Run kubectl commands mentally |
| 5 | 1 min | Review the memory trick |

> **Interview Tip:** In interviews, start with the minimal template. Add complexity only if asked.

---

## 1. Core YAML Rules You Must Memorize

### YAML Structure (Every K8s Resource)

```yaml
apiVersion: <group/version>   # What API to use
kind: <ResourceType>          # What you're creating
metadata:                     # Identity info
  name: <resource-name>       # Required: unique name
  namespace: <namespace>      # Optional: defaults to 'default'
  labels:                     # Optional: key-value pairs
    <key>: <value>
spec:                         # Desired state (varies by resource)
  <resource-specific-fields>
```

### Critical Rules

| Rule | Example | Common Mistake |
|------|---------|----------------|
| Indentation = 2 spaces | `spec:` then `  containers:` | Using tabs |
| Lists start with `-` | `- name: app` | Forgetting `-` |
| Strings usually unquoted | `name: myapp` | Over-quoting |
| Colons need space after | `key: value` | `key:value` |
| Boolean: true/false | `readOnly: true` | `"true"` (string) |

### Common apiVersion Table

| Resource | apiVersion |
|----------|------------|
| Pod, Service, ConfigMap, Secret, Namespace | `v1` |
| Deployment, ReplicaSet, DaemonSet, StatefulSet | `apps/v1` |
| Ingress | `networking.k8s.io/v1` |
| Job, CronJob | `batch/v1` |
| Role, RoleBinding | `rbac.authorization.k8s.io/v1` |
| HPA | `autoscaling/v2` |
| NetworkPolicy | `networking.k8s.io/v1` |
| PV, PVC | `v1` |
| ServiceAccount | `v1` |

> **Memory Trick:** "**PSCCSN use v1**" = Pod, Service, ConfigMap, Secret, SA, Namespace use `v1`

---

## 2. Kubectl Essentials

### Most Used Commands Table

| Action | Command |
|--------|---------|
| Apply YAML | `kubectl apply -f <file.yaml>` |
| Create from YAML | `kubectl create -f <file.yaml>` |
| Get resources | `kubectl get <resource> -n <namespace>` |
| Get all | `kubectl get all -n <namespace>` |
| Get with details | `kubectl get <resource> -o wide` |
| Get YAML output | `kubectl get <resource> <name> -o yaml` |
| Describe | `kubectl describe <resource> <name>` |
| Delete | `kubectl delete -f <file.yaml>` |
| Delete by name | `kubectl delete <resource> <name>` |
| Logs | `kubectl logs <pod-name>` |
| Logs (follow) | `kubectl logs -f <pod-name>` |
| Logs (container) | `kubectl logs <pod> -c <container>` |
| Exec into pod | `kubectl exec -it <pod> -- /bin/sh` |
| Port forward | `kubectl port-forward <pod> <local>:<pod-port>` |
| Edit live | `kubectl edit <resource> <name>` |
| Dry run | `kubectl apply -f <file> --dry-run=client` |
| Generate YAML | `kubectl create <resource> --dry-run=client -o yaml` |

### Namespace Shortcuts

```bash
# Set default namespace
kubectl config set-context --current --namespace=<namespace>

# All namespaces
kubectl get pods -A
kubectl get pods --all-namespaces
```

### Quick Resource Generation (Imperative)

```bash
# Generate Pod YAML
kubectl run <app-name> --image=<image> --dry-run=client -o yaml > pod.yaml

# Generate Deployment YAML
kubectl create deployment <app-name> --image=<image> --dry-run=client -o yaml > deploy.yaml

# Generate Service YAML
kubectl expose deployment <app-name> --port=<port> --dry-run=client -o yaml > svc.yaml

# Generate ConfigMap YAML
kubectl create configmap <name> --from-literal=<key>=<value> --dry-run=client -o yaml

# Generate Secret YAML
kubectl create secret generic <name> --from-literal=<key>=<value> --dry-run=client -o yaml
```

> **Interview Tip:** Use `--dry-run=client -o yaml` to generate templates fast, then edit.

---

## 3. Kubernetes Resources

---

### A. Namespace

**When to use:**
- Isolate environments (dev, staging, prod)
- Organize resources by team or application

#### Minimal YAML

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: <namespace>           # Required: namespace name
```

#### Interview-Ready YAML

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: <namespace>
  labels:
    env: <dev|staging|prod>   # Label for filtering
    team: <team-name>         # Owner identification
```

#### kubectl Commands

| Action | Command |
|--------|---------|
| Create | `kubectl create namespace <namespace>` |
| Apply | `kubectl apply -f namespace.yaml` |
| Get | `kubectl get namespaces` |
| Describe | `kubectl describe namespace <namespace>` |
| Delete | `kubectl delete namespace <namespace>` |

#### Debug Checklist

- [ ] Name follows DNS naming (lowercase, hyphens allowed)
- [ ] No duplicate namespace names
- [ ] Check with `kubectl get ns`

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| Using uppercase | Use lowercase only |
| Starting with number | Start with letter |
| Deleting namespace with resources | Know it deletes ALL resources inside |

#### Memory Trick

> **"Namespace = Folder"** - Just like folders organize files, namespaces organize K8s resources.

---

### B. Pod

**When to use:**
- Smallest deployable unit
- Usually created via Deployment (not directly)
- Direct Pod only for testing/debugging

#### Minimal YAML

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: <app-name>
spec:
  containers:
  - name: <container-name>    # Container identifier
    image: <image>            # Required: container image
```

#### Interview-Ready YAML

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: <app-name>
  namespace: <namespace>
  labels:
    app: <app-name>           # For service selector
spec:
  containers:
  - name: <container-name>
    image: <image>
    ports:
    - containerPort: <port>   # Port app listens on
    env:
    - name: <ENV_VAR>
      value: "<value>"
    resources:
      requests:
        memory: "64Mi"
        cpu: "100m"
      limits:
        memory: "128Mi"
        cpu: "200m"
  restartPolicy: Always       # Always|OnFailure|Never
```

#### kubectl Commands

| Action | Command |
|--------|---------|
| Create | `kubectl run <app-name> --image=<image>` |
| Apply | `kubectl apply -f pod.yaml` |
| Get | `kubectl get pods -n <namespace>` |
| Get wide | `kubectl get pods -o wide` |
| Describe | `kubectl describe pod <pod-name>` |
| Logs | `kubectl logs <pod-name>` |
| Logs follow | `kubectl logs -f <pod-name>` |
| Exec | `kubectl exec -it <pod-name> -- /bin/sh` |
| Delete | `kubectl delete pod <pod-name>` |

#### Debug Checklist

- [ ] Check pod status: `kubectl get pod <name>`
- [ ] Check events: `kubectl describe pod <name>`
- [ ] Check logs: `kubectl logs <name>`
- [ ] Check image name and tag
- [ ] Check namespace

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| `container` (singular) | Use `containers` (plural with `-`) |
| Missing `-` before container | `- name:` not `name:` |
| Wrong `containerPort` vs `port` | `containerPort` = what app listens on |

#### Memory Trick

> **"PMS"** - Pod needs **P**od metadata, **M**etadata labels, **S**pec containers

---

### C. Deployment

**When to use:**
- Production workloads (99% of cases)
- Automatic rollouts and rollbacks
- Scaling and self-healing

#### Minimal YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: <app-name>
spec:
  replicas: 1
  selector:
    matchLabels:
      app: <app-name>         # Must match template labels
  template:                   # Pod template starts here
    metadata:
      labels:
        app: <app-name>       # Must match selector
    spec:
      containers:
      - name: <container-name>
        image: <image>
```

#### Interview-Ready YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: <app-name>
  namespace: <namespace>
  labels:
    app: <app-name>
spec:
  replicas: 3                 # Number of pod replicas
  selector:
    matchLabels:
      app: <app-name>
  strategy:
    type: RollingUpdate       # RollingUpdate or Recreate
    rollingUpdate:
      maxSurge: 1             # Extra pods during update
      maxUnavailable: 0       # Keep all running during update
  template:
    metadata:
      labels:
        app: <app-name>
    spec:
      containers:
      - name: <container-name>
        image: <image>
        ports:
        - containerPort: <port>
        env:
        - name: <ENV_VAR>
          value: "<value>"
        resources:
          requests:
            memory: "64Mi"
            cpu: "100m"
          limits:
            memory: "128Mi"
            cpu: "200m"
        livenessProbe:
          httpGet:
            path: /healthz
            port: <port>
          initialDelaySeconds: 10
          periodSeconds: 5
        readinessProbe:
          httpGet:
            path: /ready
            port: <port>
          initialDelaySeconds: 5
          periodSeconds: 3
```

#### kubectl Commands

| Action | Command |
|--------|---------|
| Create | `kubectl create deployment <app-name> --image=<image>` |
| Apply | `kubectl apply -f deployment.yaml` |
| Get | `kubectl get deployments -n <namespace>` |
| Describe | `kubectl describe deployment <app-name>` |
| Scale | `kubectl scale deployment <app-name> --replicas=<n>` |
| Rollout status | `kubectl rollout status deployment/<app-name>` |
| Rollout history | `kubectl rollout history deployment/<app-name>` |
| Rollback | `kubectl rollout undo deployment/<app-name>` |
| Delete | `kubectl delete deployment <app-name>` |

#### Debug Checklist

- [ ] `selector.matchLabels` matches `template.metadata.labels`
- [ ] Check replicas count
- [ ] Check image name and tag
- [ ] Check rollout status: `kubectl rollout status deployment/<name>`

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| Selector doesn't match template labels | Must be identical |
| Missing `matchLabels` under selector | Required in apps/v1 |
| Using `apiVersion: v1` | Use `apps/v1` |

#### Memory Trick

> **"STS"** - **S**elector → **T**emplate labels → **S**ame values

---

### D. ReplicaSet

**When to use:**
- Maintains specified number of pod replicas
- Usually managed by Deployment (don't create directly)

#### Relationship

```
Deployment → manages → ReplicaSet → manages → Pods
```

| Create This | K8s Creates |
|-------------|-------------|
| Deployment | ReplicaSet + Pods |
| ReplicaSet | Pods only |
| Pod | Just the Pod |

#### Minimal YAML (Reference Only)

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: <app-name>-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: <app-name>
  template:
    metadata:
      labels:
        app: <app-name>
    spec:
      containers:
      - name: <container-name>
        image: <image>
```

> **Interview Tip:** If asked about ReplicaSet, explain it's auto-managed by Deployment. Only mention direct use for edge cases.

---

### E. Service

**When to use:**
- Expose pods internally (ClusterIP) or externally (NodePort/LoadBalancer)
- Stable network endpoint for pods

#### Service Types Table

| Type | Use Case | Accessible From |
|------|----------|-----------------|
| ClusterIP | Internal only | Inside cluster |
| NodePort | Dev/Testing | Node IP:Port |
| LoadBalancer | Production external | Cloud LB IP |
| ExternalName | DNS alias | N/A |

#### Minimal YAML (ClusterIP)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: <app-name>-svc
spec:
  selector:
    app: <app-name>           # Must match pod labels
  ports:
  - port: 80                  # Service port
    targetPort: <port>        # Container port
```

#### Interview-Ready YAML (ClusterIP)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: <app-name>-svc
  namespace: <namespace>
spec:
  type: ClusterIP             # Default type
  selector:
    app: <app-name>
  ports:
  - name: http
    port: 80                  # Port others use to reach service
    targetPort: <port>        # Port container listens on
    protocol: TCP
```

#### NodePort YAML

```yaml
apiVersion: v1
kind: Service
metadata:
  name: <app-name>-nodeport
spec:
  type: NodePort
  selector:
    app: <app-name>
  ports:
  - port: 80
    targetPort: <port>
    nodePort: 30080           # Optional: 30000-32767
```

#### LoadBalancer YAML

```yaml
apiVersion: v1
kind: Service
metadata:
  name: <app-name>-lb
spec:
  type: LoadBalancer
  selector:
    app: <app-name>
  ports:
  - port: 80
    targetPort: <port>
```

#### kubectl Commands

| Action | Command |
|--------|---------|
| Create | `kubectl expose deployment <app-name> --port=80 --target-port=<port>` |
| Apply | `kubectl apply -f service.yaml` |
| Get | `kubectl get services -n <namespace>` |
| Get wide | `kubectl get svc -o wide` |
| Describe | `kubectl describe service <svc-name>` |
| Delete | `kubectl delete service <svc-name>` |

#### Debug Checklist

- [ ] Selector matches pod labels exactly
- [ ] targetPort matches container's port
- [ ] Check endpoints: `kubectl get endpoints <svc-name>`
- [ ] Test connectivity: `kubectl run test --rm -it --image=busybox -- wget -qO- <svc-name>:<port>`

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| Selector doesn't match pod labels | Labels must match exactly |
| `targetPort` vs `port` confusion | targetPort = container port |
| NodePort outside 30000-32767 | Use valid range |

#### Memory Trick

> **"Port → Target"** - Service `port` is what you call, `targetPort` is where it goes

---

### F. ConfigMap

**When to use:**
- Store non-sensitive configuration
- Environment variables or config files
- Decouple config from image

#### Minimal YAML

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: <app-name>-config
data:
  <config-key>: <value>       # Key-value pairs
```

#### Interview-Ready YAML

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: <app-name>-config
  namespace: <namespace>
data:
  # Simple key-value
  DATABASE_HOST: "db.example.com"
  LOG_LEVEL: "info"
  
  # Multi-line config file
  app.properties: |
    server.port=8080
    server.timeout=30
```

#### Using ConfigMap in Pod

```yaml
# As environment variables
spec:
  containers:
  - name: <container-name>
    image: <image>
    env:
    - name: DATABASE_HOST
      valueFrom:
        configMapKeyRef:
          name: <app-name>-config
          key: DATABASE_HOST
    # Or load all keys
    envFrom:
    - configMapRef:
        name: <app-name>-config
```

```yaml
# As mounted volume
spec:
  containers:
  - name: <container-name>
    image: <image>
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: <app-name>-config
```

#### kubectl Commands

| Action | Command |
|--------|---------|
| Create from literal | `kubectl create configmap <name> --from-literal=<key>=<value>` |
| Create from file | `kubectl create configmap <name> --from-file=<path>` |
| Apply | `kubectl apply -f configmap.yaml` |
| Get | `kubectl get configmaps` |
| Describe | `kubectl describe configmap <name>` |
| Delete | `kubectl delete configmap <name>` |

#### Debug Checklist

- [ ] ConfigMap exists in same namespace as pod
- [ ] Key names match in pod reference
- [ ] Check data: `kubectl get configmap <name> -o yaml`

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| ConfigMap in different namespace | Must be same namespace as pod |
| Wrong key name reference | Check exact key name |
| Forgetting to restart pods | Pods need restart to pick up changes (unless using mounted volume) |

#### Memory Trick

> **"ConfigMap = Public"** - Anyone can read, never put secrets here

---

### G. Secret

**When to use:**
- Store sensitive data (passwords, tokens, keys)
- Base64 encoded (not encrypted!)
- Reference in pods like ConfigMap

#### Secret Types

| Type | Use Case |
|------|----------|
| Opaque | Generic secrets (default) |
| kubernetes.io/tls | TLS certificates |
| kubernetes.io/dockerconfigjson | Docker registry auth |

#### Minimal YAML (Generic)

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: <app-name>-secret
type: Opaque
data:
  <secret-key>: <base64-value>  # Must be base64 encoded
```

#### Interview-Ready YAML (Using stringData)

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: <app-name>-secret
  namespace: <namespace>
type: Opaque
stringData:                     # Plain text (auto-encoded)
  DB_PASSWORD: "mysecretpassword"
  API_KEY: "abc123xyz"
```

#### TLS Secret YAML

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: <app-name>-tls
type: kubernetes.io/tls
data:
  tls.crt: <base64-cert>
  tls.key: <base64-key>
```

#### Using Secret in Pod

```yaml
# As environment variable
spec:
  containers:
  - name: <container-name>
    image: <image>
    env:
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: <app-name>-secret
          key: DB_PASSWORD
```

```yaml
# As mounted volume
spec:
  containers:
  - name: <container-name>
    image: <image>
    volumeMounts:
    - name: secret-volume
      mountPath: /etc/secrets
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: <app-name>-secret
```

#### kubectl Commands

| Action | Command |
|--------|---------|
| Create generic | `kubectl create secret generic <name> --from-literal=<key>=<value>` |
| Create TLS | `kubectl create secret tls <name> --cert=<cert-file> --key=<key-file>` |
| Apply | `kubectl apply -f secret.yaml` |
| Get | `kubectl get secrets` |
| Describe | `kubectl describe secret <name>` |
| View decoded | `kubectl get secret <name> -o jsonpath='{.data.<key>}' \| base64 -d` |
| Delete | `kubectl delete secret <name>` |

#### Base64 Encode/Decode

```bash
# Encode
echo -n 'mypassword' | base64
# Output: bXlwYXNzd29yZA==

# Decode
echo 'bXlwYXNzd29yZA==' | base64 -d
# Output: mypassword
```

#### Debug Checklist

- [ ] Secret exists in same namespace
- [ ] Key name matches reference
- [ ] Value is base64 encoded (if using `data:`)
- [ ] Use `stringData:` to avoid encoding issues

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| Plain text in `data:` field | Use `stringData:` or base64 encode |
| Including newline in base64 | Use `echo -n` (no newline) |
| Secret in different namespace | Must be same namespace |

#### Memory Trick

> **"stringData = Simple, data = Difficult"** - Use `stringData` to avoid base64 hassle

---

### H. Ingress

**When to use:**
- HTTP/HTTPS routing to services
- Host-based or path-based routing
- TLS termination

#### Minimal YAML

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: <app-name>-ingress
spec:
  rules:
  - host: <app.example.com>
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: <app-name>-svc
            port:
              number: 80
```

#### Interview-Ready YAML

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: <app-name>-ingress
  namespace: <namespace>
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx       # Specify ingress controller
  tls:
  - hosts:
    - <app.example.com>
    secretName: <app-name>-tls  # TLS secret
  rules:
  - host: <app.example.com>
    http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: <api-svc>
            port:
              number: 80
      - path: /
        pathType: Prefix
        backend:
          service:
            name: <frontend-svc>
            port:
              number: 80
```

#### pathType Options

| Type | Behavior |
|------|----------|
| Prefix | Matches URL path prefix |
| Exact | Matches exact path only |
| ImplementationSpecific | Depends on IngressClass |

#### kubectl Commands

| Action | Command |
|--------|---------|
| Apply | `kubectl apply -f ingress.yaml` |
| Get | `kubectl get ingress` |
| Describe | `kubectl describe ingress <name>` |
| Delete | `kubectl delete ingress <name>` |

#### Debug Checklist

- [ ] Ingress controller is installed
- [ ] Service exists and has endpoints
- [ ] Host DNS resolves to ingress
- [ ] Check ingress address: `kubectl get ingress`
- [ ] Check events: `kubectl describe ingress <name>`

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| No ingress controller | Install nginx-ingress or similar |
| Service name/port wrong | Verify service exists |
| Missing `pathType` | Required in networking.k8s.io/v1 |

#### Memory Trick

> **"Ingress = Front Door"** - Routes external traffic to the right room (service)

---

### I. ServiceAccount

**When to use:**
- Identity for pods to access K8s API
- Attach RBAC permissions
- Default: `default` SA in each namespace

#### Minimal YAML

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: <sa-name>
```

#### Interview-Ready YAML

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: <sa-name>
  namespace: <namespace>
  labels:
    app: <app-name>
automountServiceAccountToken: true  # Mount token in pods
```

#### Using ServiceAccount in Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: <app-name>
spec:
  serviceAccountName: <sa-name>   # Use this SA
  containers:
  - name: <container-name>
    image: <image>
```

#### kubectl Commands

| Action | Command |
|--------|---------|
| Create | `kubectl create serviceaccount <sa-name>` |
| Apply | `kubectl apply -f sa.yaml` |
| Get | `kubectl get serviceaccounts` |
| Describe | `kubectl describe sa <sa-name>` |
| Delete | `kubectl delete sa <sa-name>` |

#### Debug Checklist

- [ ] SA exists in same namespace
- [ ] Pod references correct SA name
- [ ] Check SA: `kubectl get sa -n <namespace>`

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| SA in different namespace | Must be same namespace as pod |
| Typo in SA name | Verify exact name |
| Forgetting RBAC binding | SA needs Role/ClusterRole bound |

#### Memory Trick

> **"ServiceAccount = Badge"** - Identifies who/what the pod is

---

### J. RBAC: Role + RoleBinding

**When to use:**
- Control who can do what in cluster
- Role = namespace-scoped permissions
- ClusterRole = cluster-wide permissions

#### Role Minimal YAML

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: <role-name>
  namespace: <namespace>
rules:
- apiGroups: [""]             # "" = core API group
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

#### Role Interview-Ready YAML

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: <role-name>
  namespace: <namespace>
rules:
- apiGroups: [""]
  resources: ["pods", "pods/log"]
  verbs: ["get", "list", "watch"]
- apiGroups: ["apps"]
  resources: ["deployments"]
  verbs: ["get", "list", "watch", "create", "update", "delete"]
- apiGroups: [""]
  resources: ["configmaps", "secrets"]
  verbs: ["get", "list"]
```

#### RoleBinding YAML

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: <binding-name>
  namespace: <namespace>
subjects:
- kind: ServiceAccount
  name: <sa-name>
  namespace: <namespace>
roleRef:
  kind: Role
  name: <role-name>
  apiGroup: rbac.authorization.k8s.io
```

#### Common Verbs

| Verb | Action |
|------|--------|
| get | Read single resource |
| list | Read multiple resources |
| watch | Stream changes |
| create | Create new |
| update | Modify existing |
| patch | Partial update |
| delete | Remove resource |
| * | All verbs |

#### kubectl Commands

| Action | Command |
|--------|---------|
| Create role | `kubectl create role <name> --verb=get,list --resource=pods` |
| Create binding | `kubectl create rolebinding <name> --role=<role> --serviceaccount=<ns>:<sa>` |
| Apply | `kubectl apply -f role.yaml` |
| Get roles | `kubectl get roles` |
| Get bindings | `kubectl get rolebindings` |
| Check access | `kubectl auth can-i <verb> <resource> --as=system:serviceaccount:<ns>:<sa>` |

#### Debug Checklist

- [ ] Role and RoleBinding in same namespace
- [ ] Subject matches SA name and namespace
- [ ] roleRef name matches Role name
- [ ] Test with `kubectl auth can-i`

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| Wrong apiGroup for resources | `""` for core, `apps` for deployments |
| RoleBinding in different namespace | Must be same namespace |
| Missing namespace in subject | Include namespace for SA |

#### Memory Trick

> **"Role = Menu, Binding = Waiter"** - Role lists what's available, Binding serves it to the customer (SA)

---

### K. Resource Requests/Limits

**When to use:**
- Control pod resource consumption
- Requests = minimum guaranteed
- Limits = maximum allowed

#### YAML in Container Spec

```yaml
spec:
  containers:
  - name: <container-name>
    image: <image>
    resources:
      requests:
        memory: "64Mi"        # Minimum memory
        cpu: "100m"           # Minimum CPU (100 millicores)
      limits:
        memory: "128Mi"       # Maximum memory
        cpu: "200m"           # Maximum CPU
```

#### Resource Units

| Resource | Unit | Example |
|----------|------|---------|
| CPU | millicores | `100m` = 0.1 CPU |
| CPU | cores | `1` = 1 CPU |
| Memory | bytes | `128Mi` = 128 mebibytes |
| Memory | gigabytes | `1Gi` = 1 gibibyte |

#### Quick Reference

| Setting | Effect |
|---------|--------|
| Request only | Scheduling guarantee, no cap |
| Limit only | Capped, request = limit |
| Both | Best practice |

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| Limit < Request | Limit must be >= Request |
| Wrong memory unit | Use `Mi` or `Gi`, not `MB` or `GB` |
| Forgetting requests | Pods may get evicted under pressure |

#### Memory Trick

> **"Request = Reservation, Limit = Maximum"** - Like a hotel room booking

---

### L. Liveness/Readiness Probes

**When to use:**
- Liveness: Is the container alive? (restart if not)
- Readiness: Is it ready to serve traffic? (remove from service if not)

#### Probe Types

| Type | Use Case |
|------|----------|
| httpGet | HTTP endpoint check |
| tcpSocket | TCP port check |
| exec | Command execution |

#### HTTP Probe YAML

```yaml
spec:
  containers:
  - name: <container-name>
    image: <image>
    ports:
    - containerPort: <port>
    livenessProbe:
      httpGet:
        path: /healthz        # Health endpoint
        port: <port>
      initialDelaySeconds: 10 # Wait before first check
      periodSeconds: 5        # Check every N seconds
      timeoutSeconds: 1       # Timeout for check
      failureThreshold: 3     # Failures before action
    readinessProbe:
      httpGet:
        path: /ready
        port: <port>
      initialDelaySeconds: 5
      periodSeconds: 3
```

#### TCP Probe YAML

```yaml
livenessProbe:
  tcpSocket:
    port: <port>
  initialDelaySeconds: 10
  periodSeconds: 5
```

#### Exec Probe YAML

```yaml
livenessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  initialDelaySeconds: 5
  periodSeconds: 5
```

#### Probe Settings

| Setting | Default | Description |
|---------|---------|-------------|
| initialDelaySeconds | 0 | Wait before first probe |
| periodSeconds | 10 | Time between probes |
| timeoutSeconds | 1 | Probe timeout |
| successThreshold | 1 | Successes to be healthy |
| failureThreshold | 3 | Failures before unhealthy |

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| initialDelaySeconds too short | App may not be ready |
| Same probe for liveness/readiness | Readiness can be stricter |
| Liveness restarts healthy pod | Check endpoint actually indicates health |

#### Memory Trick

> **"Liveness = Alive?, Readiness = Ready?"** - Both questions, different actions

---

### M. Volumes

**When to use:**
- Share data between containers
- Persist data beyond container lifecycle
- Mount config files

#### Volume Types

| Type | Persistence | Use Case |
|------|-------------|----------|
| emptyDir | Pod lifetime | Temp storage, sharing |
| hostPath | Node | Access node files (avoid!) |
| configMap | External | Config files |
| secret | External | Sensitive files |
| persistentVolumeClaim | Persistent | Database storage |

#### emptyDir YAML

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: <app-name>
spec:
  containers:
  - name: <container-name>
    image: <image>
    volumeMounts:
    - name: cache-volume
      mountPath: /cache       # Mount path in container
  volumes:
  - name: cache-volume
    emptyDir: {}              # Empty on pod start
```

#### hostPath YAML (Use with caution!)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: <app-name>
spec:
  containers:
  - name: <container-name>
    image: <image>
    volumeMounts:
    - name: host-volume
      mountPath: /host-data
  volumes:
  - name: host-volume
    hostPath:
      path: /data             # Path on node
      type: Directory
```

> **Warning:** hostPath ties pod to specific node and poses security risks!

#### PVC YAML (See next section for full example)

```yaml
volumes:
- name: data-volume
  persistentVolumeClaim:
    claimName: <pvc-name>
```

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| Volume name mismatch | volumeMounts.name must match volumes.name |
| Missing volumes section | Need both volumeMounts AND volumes |
| Using hostPath in prod | Use PVC instead |

#### Memory Trick

> **"Mount = Attach, Volume = Disk"** - Mount attaches volume to container path

---

### N. PVC + PV

**When to use:**
- Persistent storage that survives pod restarts
- Database storage
- PV = actual storage, PVC = request for storage

#### PersistentVolume (Admin creates)

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <pv-name>
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce           # RWO, ROX, or RWX
  persistentVolumeReclaimPolicy: Retain
  storageClassName: standard
  hostPath:                   # Example: hostPath (use cloud storage in prod)
    path: /mnt/data
```

#### PersistentVolumeClaim (Developer creates)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <pvc-name>
  namespace: <namespace>
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  storageClassName: standard  # Must match PV or use dynamic provisioning
```

#### Using PVC in Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: <app-name>
spec:
  containers:
  - name: <container-name>
    image: <image>
    volumeMounts:
    - name: data-volume
      mountPath: /data
  volumes:
  - name: data-volume
    persistentVolumeClaim:
      claimName: <pvc-name>   # Reference PVC
```

#### Access Modes

| Mode | Abbreviation | Description |
|------|--------------|-------------|
| ReadWriteOnce | RWO | One node read/write |
| ReadOnlyMany | ROX | Many nodes read |
| ReadWriteMany | RWX | Many nodes read/write |

#### kubectl Commands

| Action | Command |
|--------|---------|
| Get PV | `kubectl get pv` |
| Get PVC | `kubectl get pvc -n <namespace>` |
| Describe PVC | `kubectl describe pvc <pvc-name>` |
| Delete PVC | `kubectl delete pvc <pvc-name>` |

#### Debug Checklist

- [ ] PVC status is Bound (not Pending)
- [ ] StorageClass exists or matches PV
- [ ] Access mode compatible with PV
- [ ] Requested size <= PV capacity

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| PVC stays Pending | Check StorageClass and PV availability |
| Access mode mismatch | PVC mode must be subset of PV modes |
| Wrong storageClassName | Must match exactly |

#### Memory Trick

> **"PV = House, PVC = Lease"** - PV is the storage, PVC is your claim to use it

---

### O. StatefulSet

**When to use:**
- Stateful applications (databases)
- Stable network identity
- Ordered deployment/scaling

#### Minimal YAML

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: <app-name>
spec:
  serviceName: <headless-svc>   # Headless service for DNS
  replicas: 3
  selector:
    matchLabels:
      app: <app-name>
  template:
    metadata:
      labels:
        app: <app-name>
    spec:
      containers:
      - name: <container-name>
        image: <image>
        ports:
        - containerPort: <port>
```

#### Interview-Ready YAML

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: <app-name>
  namespace: <namespace>
spec:
  serviceName: <app-name>-headless
  replicas: 3
  selector:
    matchLabels:
      app: <app-name>
  template:
    metadata:
      labels:
        app: <app-name>
    spec:
      containers:
      - name: <container-name>
        image: <image>
        ports:
        - containerPort: <port>
        volumeMounts:
        - name: data
          mountPath: /data
  volumeClaimTemplates:         # Auto-create PVC per pod
  - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 10Gi
```

#### Headless Service (Required)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: <app-name>-headless
spec:
  clusterIP: None               # Headless!
  selector:
    app: <app-name>
  ports:
  - port: <port>
```

#### StatefulSet DNS

| Pod Name | DNS |
|----------|-----|
| app-0 | app-0.app-headless.namespace.svc.cluster.local |
| app-1 | app-1.app-headless.namespace.svc.cluster.local |

#### kubectl Commands

| Action | Command |
|--------|---------|
| Apply | `kubectl apply -f statefulset.yaml` |
| Get | `kubectl get statefulsets` |
| Describe | `kubectl describe statefulset <name>` |
| Scale | `kubectl scale statefulset <name> --replicas=<n>` |
| Delete | `kubectl delete statefulset <name>` |

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| Missing headless service | `clusterIP: None` required |
| serviceName doesn't match service | Must match exactly |
| Expecting random pod names | Names are ordered: app-0, app-1 |

#### Memory Trick

> **"StatefulSet = Named Pods"** - Each pod has identity: name-0, name-1, etc.

---

### P. DaemonSet

**When to use:**
- Run on ALL (or selected) nodes
- Log collectors, monitoring agents
- Node-level operations

#### Minimal YAML

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: <app-name>
spec:
  selector:
    matchLabels:
      app: <app-name>
  template:
    metadata:
      labels:
        app: <app-name>
    spec:
      containers:
      - name: <container-name>
        image: <image>
```

#### Interview-Ready YAML

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: <app-name>
  namespace: <namespace>
spec:
  selector:
    matchLabels:
      app: <app-name>
  template:
    metadata:
      labels:
        app: <app-name>
    spec:
      tolerations:              # Run on master nodes too
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: <container-name>
        image: <image>
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 100Mi
```

#### kubectl Commands

| Action | Command |
|--------|---------|
| Apply | `kubectl apply -f daemonset.yaml` |
| Get | `kubectl get daemonsets` |
| Describe | `kubectl describe daemonset <name>` |
| Delete | `kubectl delete daemonset <name>` |

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| Using replicas field | DaemonSet doesn't have replicas |
| Not running on masters | Add toleration for master taint |
| Expecting same count as nodes | May differ due to taints |

#### Memory Trick

> **"DaemonSet = One Per Node"** - Like a security guard at every entrance

---

### Q. Job + CronJob

**When to use:**
- Job: One-time task (batch processing)
- CronJob: Scheduled recurring tasks

#### Job Minimal YAML

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: <job-name>
spec:
  template:
    spec:
      containers:
      - name: <container-name>
        image: <image>
        command: ["<command>"]
      restartPolicy: Never      # Never or OnFailure
```

#### Job Interview-Ready YAML

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: <job-name>
  namespace: <namespace>
spec:
  completions: 1                # How many successful pods
  parallelism: 1                # How many pods at once
  backoffLimit: 4               # Retries before failing
  activeDeadlineSeconds: 300    # Timeout
  template:
    spec:
      containers:
      - name: <container-name>
        image: <image>
        command: ["<command>"]
        args: ["<arg1>", "<arg2>"]
      restartPolicy: Never
```

#### CronJob YAML

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: <cronjob-name>
  namespace: <namespace>
spec:
  schedule: "0 * * * *"         # Cron expression
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: <container-name>
            image: <image>
            command: ["<command>"]
          restartPolicy: OnFailure
```

#### Cron Schedule Format

```
┌───────────── minute (0 - 59)
│ ┌───────────── hour (0 - 23)
│ │ ┌───────────── day of month (1 - 31)
│ │ │ ┌───────────── month (1 - 12)
│ │ │ │ ┌───────────── day of week (0 - 6) (Sunday = 0)
│ │ │ │ │
* * * * *
```

| Schedule | Meaning |
|----------|---------|
| `*/5 * * * *` | Every 5 minutes |
| `0 * * * *` | Every hour |
| `0 0 * * *` | Daily at midnight |
| `0 0 * * 0` | Weekly on Sunday |

#### kubectl Commands

| Action | Command |
|--------|---------|
| Apply Job | `kubectl apply -f job.yaml` |
| Get Jobs | `kubectl get jobs` |
| Get CronJobs | `kubectl get cronjobs` |
| Describe | `kubectl describe job <name>` |
| Logs | `kubectl logs job/<job-name>` |
| Delete | `kubectl delete job <name>` |

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| `restartPolicy: Always` | Jobs need `Never` or `OnFailure` |
| Wrong cron syntax | Test with crontab.guru |
| Job template nesting | CronJob has jobTemplate.spec.template |

#### Memory Trick

> **"Job = Once, CronJob = Repeat"** - Job runs once, CronJob runs on schedule

---

### R. HPA

**When to use:**
- Auto-scale deployments based on metrics
- Handle variable load
- Cost optimization

#### Minimal YAML

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: <app-name>-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: <app-name>
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 80
```

#### Interview-Ready YAML

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: <app-name>-hpa
  namespace: <namespace>
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: <app-name>
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 80
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300
```

#### kubectl Commands

| Action | Command |
|--------|---------|
| Create | `kubectl autoscale deployment <name> --min=2 --max=10 --cpu-percent=80` |
| Apply | `kubectl apply -f hpa.yaml` |
| Get | `kubectl get hpa` |
| Describe | `kubectl describe hpa <name>` |
| Delete | `kubectl delete hpa <name>` |

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| No resource requests | Pods MUST have CPU/memory requests |
| Metrics server missing | Install metrics-server |
| Target deployment name wrong | Must match exactly |

#### Memory Trick

> **"HPA = Auto Pilot"** - Automatically adjusts replicas based on load

---

### S. NetworkPolicy

**When to use:**
- Control pod-to-pod traffic
- Implement zero-trust
- Isolate namespaces

#### Minimal YAML (Allow specific ingress)

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: <policy-name>
  namespace: <namespace>
spec:
  podSelector:
    matchLabels:
      app: <app-name>         # Apply to these pods
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: <allowed-app>  # Allow from these pods
    ports:
    - port: <port>
```

#### Interview-Ready YAML (Allow from namespace)

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: <policy-name>
  namespace: <namespace>
spec:
  podSelector:
    matchLabels:
      app: <app-name>
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          env: production
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 80
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: database
    ports:
    - protocol: TCP
      port: 5432
```

#### Deny All Traffic

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
  namespace: <namespace>
spec:
  podSelector: {}             # All pods in namespace
  policyTypes:
  - Ingress
  - Egress
```

#### kubectl Commands

| Action | Command |
|--------|---------|
| Apply | `kubectl apply -f networkpolicy.yaml` |
| Get | `kubectl get networkpolicies` |
| Describe | `kubectl describe networkpolicy <name>` |
| Delete | `kubectl delete networkpolicy <name>` |

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| CNI doesn't support policies | Use Calico, Cilium, etc. |
| Empty `from:` blocks all | Missing `from:` allows all |
| Forgetting egress for DNS | Allow egress to kube-system for DNS |

#### Memory Trick

> **"NetworkPolicy = Firewall"** - Rules for who can talk to whom

---

### T. Helm Basics

**When to use:**
- Package and deploy applications
- Manage releases
- Template K8s manifests

#### Helm Directory Structure

```
mychart/
├── Chart.yaml          # Chart metadata
├── values.yaml         # Default values
├── templates/          # Template files
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── _helpers.tpl    # Template helpers
│   └── NOTES.txt       # Post-install notes
```

#### Chart.yaml

```yaml
apiVersion: v2
name: <chart-name>
description: A Helm chart for <app-name>
version: 1.0.0              # Chart version
appVersion: "1.0.0"         # App version
```

#### values.yaml

```yaml
# Default values
replicaCount: 2

image:
  repository: <image>
  tag: "latest"
  pullPolicy: IfNotPresent

service:
  type: ClusterIP
  port: 80

resources:
  limits:
    cpu: 200m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 64Mi
```

#### templates/deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}
  labels:
    app: {{ .Chart.Name }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ .Chart.Name }}
  template:
    metadata:
      labels:
        app: {{ .Chart.Name }}
    spec:
      containers:
      - name: {{ .Chart.Name }}
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
        ports:
        - containerPort: {{ .Values.service.port }}
        resources:
          {{- toYaml .Values.resources | nindent 12 }}
```

#### Helm Commands

| Action | Command |
|--------|---------|
| Create chart | `helm create <chart-name>` |
| Install | `helm install <release-name> <chart-path>` |
| Install with values | `helm install <release-name> <chart-path> -f values.yaml` |
| Upgrade | `helm upgrade <release-name> <chart-path>` |
| List releases | `helm list` |
| Uninstall | `helm uninstall <release-name>` |
| Template (dry-run) | `helm template <release-name> <chart-path>` |
| Lint | `helm lint <chart-path>` |

#### Helm Template Syntax

| Syntax | Description |
|--------|-------------|
| `{{ .Values.key }}` | Access values.yaml |
| `{{ .Release.Name }}` | Release name |
| `{{ .Chart.Name }}` | Chart name |
| `{{ include "helper" . }}` | Include helper |
| `{{- toYaml .Values.x \| nindent 4 }}` | YAML with indent |

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| Wrong template indentation | Use `nindent` function |
| Missing `{{- }}` for whitespace | Use `-` to trim |
| Values path typo | Check values.yaml structure |

#### Memory Trick

> **"Helm = Package Manager"** - Like apt/yum but for K8s

---

## 4. OpenShift Resources + oc CLI

---

### A. Project

**OpenShift Project = Kubernetes Namespace + extras**

| Feature | Namespace | Project |
|---------|-----------|---------|
| Isolation | Yes | Yes |
| RBAC default | No | Yes (auto-creates) |
| Quotas UI | Manual | Integrated |
| Self-service | kubectl | oc new-project |

#### Create Project

```bash
# CLI (preferred)
oc new-project <project-name>
oc new-project <project-name> --description="My app" --display-name="My App"

# YAML (same as namespace)
apiVersion: v1
kind: Namespace
metadata:
  name: <project-name>
```

#### oc Project Commands

| Action | Command |
|--------|---------|
| Create | `oc new-project <name>` |
| Switch | `oc project <name>` |
| List | `oc projects` |
| Current | `oc project` |
| Delete | `oc delete project <name>` |

---

### B. DeploymentConfig vs Deployment

| Feature | DeploymentConfig (Legacy) | Deployment (Recommended) |
|---------|---------------------------|--------------------------|
| API | `apps.openshift.io/v1` | `apps/v1` |
| Rolling updates | Yes | Yes |
| Triggers | ImageStream, Config | Manual |
| Rollback | `oc rollback` | `kubectl rollout undo` |
| Future | Deprecated | Active development |

> **Interview Tip:** Always mention Deployment is recommended. DeploymentConfig is legacy.

#### DeploymentConfig YAML (Legacy Reference)

```yaml
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: <app-name>
spec:
  replicas: 2
  selector:
    app: <app-name>
  template:
    metadata:
      labels:
        app: <app-name>
    spec:
      containers:
      - name: <container-name>
        image: <image>
        ports:
        - containerPort: <port>
  triggers:
  - type: ConfigChange
  - type: ImageChange
    imageChangeParams:
      automatic: true
      containerNames:
      - <container-name>
      from:
        kind: ImageStreamTag
        name: <image>:latest
```

> **Use Deployment (from Section 3.C) for new applications.**

---

### C. Route

**When to use:**
- Expose services externally in OpenShift
- Like Ingress but OpenShift-native
- TLS termination

#### Minimal YAML

```yaml
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: <app-name>-route
spec:
  to:
    kind: Service
    name: <app-name>-svc
  port:
    targetPort: <port>
```

#### Interview-Ready YAML (TLS)

```yaml
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: <app-name>-route
  namespace: <namespace>
spec:
  host: <app.example.com>       # Optional: custom hostname
  to:
    kind: Service
    name: <app-name>-svc
    weight: 100
  port:
    targetPort: <port>          # Service port name or number
  tls:
    termination: edge           # edge, passthrough, reencrypt
    insecureEdgeTerminationPolicy: Redirect
```

#### TLS Termination Types

| Type | Description |
|------|-------------|
| edge | TLS ends at router |
| passthrough | TLS to pod (pod handles cert) |
| reencrypt | TLS at router, new TLS to pod |

#### oc Route Commands

| Action | Command |
|--------|---------|
| Create | `oc expose service <svc-name>` |
| Create with hostname | `oc expose service <svc-name> --hostname=<host>` |
| Get | `oc get routes` |
| Describe | `oc describe route <name>` |
| Delete | `oc delete route <name>` |

#### Debug Checklist

- [ ] Service exists and has endpoints
- [ ] Route hostname resolves
- [ ] Check route status: `oc get route`
- [ ] Check router logs if needed

#### Top 3 Mistakes

| Mistake | Fix |
|---------|-----|
| targetPort mismatch | Match service port |
| Wrong TLS termination | Use appropriate type |
| Service has no endpoints | Check pod labels match service selector |

---

### D. BuildConfig + ImageStream

**When to use:**
- Build images from source in OpenShift
- CI/CD pipelines
- ImageStream tracks image versions

#### BuildConfig YAML (Source-to-Image)

```yaml
apiVersion: build.openshift.io/v1
kind: BuildConfig
metadata:
  name: <app-name>-build
spec:
  source:
    type: Git
    git:
      uri: <https://github.com/user/repo.git>
      ref: main
  strategy:
    type: Source
    sourceStrategy:
      from:
        kind: ImageStreamTag
        name: nodejs:16-ubi8    # Base image
        namespace: openshift
  output:
    to:
      kind: ImageStreamTag
      name: <app-name>:latest
  triggers:
  - type: ConfigChange
  - type: GitHub
    github:
      secret: <webhook-secret>
```

#### ImageStream YAML

```yaml
apiVersion: image.openshift.io/v1
kind: ImageStream
metadata:
  name: <app-name>
spec:
  lookupPolicy:
    local: true
```

#### oc Build Commands

| Action | Command |
|--------|---------|
| Create from Git | `oc new-app <git-url>` |
| Create build | `oc new-build <git-url>` |
| Start build | `oc start-build <buildconfig-name>` |
| Build from local | `oc start-build <bc-name> --from-dir=.` |
| Build logs | `oc logs -f bc/<buildconfig-name>` |
| Get builds | `oc get builds` |
| Get ImageStreams | `oc get imagestreams` |

---

### E. SecurityContextConstraints

**What it is:**
- OpenShift security policy
- Controls what pods can do (run as root, use hostPath, etc.)
- More restrictive than K8s by default

#### Common SCCs

| SCC | Description |
|-----|-------------|
| restricted | Default, most restrictive |
| anyuid | Run as any user ID |
| privileged | Full access (avoid!) |
| hostnetwork | Access host network |

#### Interview Answer

> "In OpenShift, if a pod fails due to permission errors, check the SCC. Use `oc get scc` to list SCCs and `oc adm policy add-scc-to-user <scc> -z <serviceaccount>` to grant access."

#### oc SCC Commands

| Action | Command |
|--------|---------|
| List SCCs | `oc get scc` |
| Describe SCC | `oc describe scc <name>` |
| Add SCC to SA | `oc adm policy add-scc-to-user <scc> -z <sa-name>` |
| Remove SCC | `oc adm policy remove-scc-from-user <scc> -z <sa-name>` |
| Check pod SCC | `oc get pod <name> -o yaml \| grep scc` |

---

### F. oc Commands

#### Most Used oc Commands Table

| Action | Command |
|--------|---------|
| Login | `oc login <cluster-url>` |
| Current user | `oc whoami` |
| Current context | `oc whoami --show-context` |
| New project | `oc new-project <name>` |
| New app from image | `oc new-app <image>` |
| New app from Git | `oc new-app <git-url>` |
| Expose service | `oc expose svc/<service-name>` |
| Start build | `oc start-build <bc-name>` |
| Build logs | `oc logs -f bc/<bc-name>` |
| Rollout status | `oc rollout status dc/<name>` |
| Rollout latest | `oc rollout latest dc/<name>` |
| Rollback | `oc rollback dc/<name>` |
| Remote shell | `oc rsh <pod-name>` |
| Port forward | `oc port-forward <pod> <local>:<pod-port>` |
| Debug pod | `oc debug <pod-name>` |
| Get all | `oc get all` |

#### oc vs kubectl

| Feature | oc | kubectl |
|---------|-----|---------|
| Works in OpenShift | Yes | Yes |
| Works in vanilla K8s | No | Yes |
| Project support | Yes | Namespace only |
| Build commands | Yes | No |
| Route commands | Yes | No |
| Login command | Yes | No |

---

## 5. Fast Copy Templates Section

### Kubernetes Stack (Copy-Paste Ready)

```yaml
---
# Namespace
apiVersion: v1
kind: Namespace
metadata:
  name: <namespace>
---
# ConfigMap
apiVersion: v1
kind: ConfigMap
metadata:
  name: <app-name>-config
  namespace: <namespace>
data:
  APP_ENV: "production"
---
# Secret
apiVersion: v1
kind: Secret
metadata:
  name: <app-name>-secret
  namespace: <namespace>
type: Opaque
stringData:
  DB_PASSWORD: "<password>"
---
# Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: <app-name>
  namespace: <namespace>
spec:
  replicas: 2
  selector:
    matchLabels:
      app: <app-name>
  template:
    metadata:
      labels:
        app: <app-name>
    spec:
      containers:
      - name: <app-name>
        image: <image>
        ports:
        - containerPort: <port>
        envFrom:
        - configMapRef:
            name: <app-name>-config
        - secretRef:
            name: <app-name>-secret
        resources:
          requests:
            memory: "64Mi"
            cpu: "100m"
          limits:
            memory: "128Mi"
            cpu: "200m"
---
# Service
apiVersion: v1
kind: Service
metadata:
  name: <app-name>-svc
  namespace: <namespace>
spec:
  selector:
    app: <app-name>
  ports:
  - port: 80
    targetPort: <port>
---
# Ingress
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: <app-name>-ingress
  namespace: <namespace>
spec:
  ingressClassName: nginx
  rules:
  - host: <app.example.com>
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: <app-name>-svc
            port:
              number: 80
```

### OpenShift Stack (Copy-Paste Ready)

```yaml
---
# Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: <app-name>
spec:
  replicas: 2
  selector:
    matchLabels:
      app: <app-name>
  template:
    metadata:
      labels:
        app: <app-name>
    spec:
      containers:
      - name: <app-name>
        image: <image>
        ports:
        - containerPort: <port>
---
# Service
apiVersion: v1
kind: Service
metadata:
  name: <app-name>-svc
spec:
  selector:
    app: <app-name>
  ports:
  - port: 80
    targetPort: <port>
---
# Route
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: <app-name>-route
spec:
  to:
    kind: Service
    name: <app-name>-svc
  port:
    targetPort: <port>
  tls:
    termination: edge
    insecureEdgeTerminationPolicy: Redirect
```

---

## 6. Debugging Playbook

### Pod Not Starting (CrashLoopBackOff)

| Step | Command | What to Look For |
|------|---------|------------------|
| 1 | `kubectl get pods` | Status: CrashLoopBackOff |
| 2 | `kubectl describe pod <name>` | Events section, restart count |
| 3 | `kubectl logs <pod-name>` | Application errors |
| 4 | `kubectl logs <pod-name> --previous` | Logs from crashed container |

**Common Causes:**
- Application crash/error
- Missing config/secrets
- Wrong command/args
- Health check failing too early

---

### ImagePullBackOff

| Step | Command | What to Look For |
|------|---------|------------------|
| 1 | `kubectl describe pod <name>` | Events: "Failed to pull image" |
| 2 | Check image name | Typo in image:tag |
| 3 | Check registry | Is it private? |
| 4 | Check secret | `imagePullSecrets` configured? |

**Fix for Private Registry:**

```yaml
spec:
  imagePullSecrets:
  - name: <registry-secret>
  containers:
  - name: <name>
    image: <private-registry>/<image>:<tag>
```

---

### Service Not Reachable

| Step | Command | What to Look For |
|------|---------|------------------|
| 1 | `kubectl get endpoints <svc>` | Empty = no pods matched |
| 2 | `kubectl get pods --show-labels` | Pod labels |
| 3 | `kubectl describe svc <svc>` | Selector matches labels? |
| 4 | `kubectl exec -it <pod> -- curl <svc>:<port>` | Test from inside |

**Common Causes:**
- Selector doesn't match pod labels
- targetPort doesn't match container port
- Pod not running/ready

---

### Ingress/Route Issues

| Step | Command | What to Look For |
|------|---------|------------------|
| 1 | `kubectl get ingress` | ADDRESS populated? |
| 2 | `kubectl describe ingress <name>` | Backend service exists? |
| 3 | `kubectl get svc <backend-svc>` | Service has endpoints? |
| 4 | Check DNS | Host resolves to ingress? |

**OpenShift Route:**
```bash
oc get route
oc describe route <name>
```

---

### RBAC Forbidden Errors

| Step | Command | What to Look For |
|------|---------|------------------|
| 1 | `kubectl auth can-i <verb> <resource>` | Yes/No |
| 2 | `kubectl auth can-i <verb> <resource> --as=system:serviceaccount:<ns>:<sa>` | Test as SA |
| 3 | `kubectl get rolebindings -n <ns>` | Binding exists? |
| 4 | `kubectl describe rolebinding <name>` | Correct role/subject? |

**Common Fixes:**
```bash
# Create role binding
kubectl create rolebinding <name> \
  --role=<role-name> \
  --serviceaccount=<namespace>:<sa-name> \
  -n <namespace>
```

---

## 7. Interview Drills

### Drill 1: Create a Namespace
**Problem:** Create namespace `interview-app`

**Files:** `namespace.yaml`

**Commands:**
```bash
kubectl apply -f namespace.yaml
kubectl get ns interview-app
```

**Expected Output:** Namespace created, STATUS: Active

---

### Drill 2: Deploy nginx
**Problem:** Deploy nginx:1.21 with 2 replicas in namespace `interview-app`

**Files:** `deployment.yaml`

**Commands:**
```bash
kubectl apply -f deployment.yaml
kubectl get pods -n interview-app
```

**Expected Output:** 2 pods running, STATUS: Running

---

### Drill 3: Expose with Service
**Problem:** Create ClusterIP service for nginx deployment on port 80

**Files:** `service.yaml`

**Commands:**
```bash
kubectl apply -f service.yaml
kubectl get svc -n interview-app
kubectl get endpoints -n interview-app
```

**Expected Output:** Service created with 2 endpoints

---

### Drill 4: Create ConfigMap
**Problem:** Create ConfigMap with `ENV=production` and `LOG_LEVEL=info`

**Files:** `configmap.yaml`

**Commands:**
```bash
kubectl apply -f configmap.yaml
kubectl describe configmap -n interview-app
```

**Expected Output:** ConfigMap with 2 data keys

---

### Drill 5: Create Secret
**Problem:** Create Secret with `DB_PASSWORD=secret123`

**Files:** `secret.yaml`

**Commands:**
```bash
kubectl apply -f secret.yaml
kubectl get secret -n interview-app
```

**Expected Output:** Secret type Opaque, 1 data item

---

### Drill 6: Add Probes
**Problem:** Add liveness probe (HTTP /healthz) and readiness probe (HTTP /ready) to nginx deployment

**Files:** Update `deployment.yaml`

**Commands:**
```bash
kubectl apply -f deployment.yaml
kubectl describe pod <pod-name> -n interview-app
```

**Expected Output:** Probes visible in pod description

---

### Drill 7: Resource Limits
**Problem:** Add requests (cpu: 100m, memory: 64Mi) and limits (cpu: 200m, memory: 128Mi)

**Files:** Update `deployment.yaml`

**Commands:**
```bash
kubectl apply -f deployment.yaml
kubectl describe pod <pod-name> -n interview-app | grep -A5 Limits
```

**Expected Output:** Resources visible in pod spec

---

### Drill 8: Create Ingress
**Problem:** Create Ingress for nginx service at host `nginx.example.com`

**Files:** `ingress.yaml`

**Commands:**
```bash
kubectl apply -f ingress.yaml
kubectl get ingress -n interview-app
```

**Expected Output:** Ingress created with host

---

### Drill 9: ServiceAccount + RBAC
**Problem:** Create SA `app-sa`, Role with pod read access, bind them

**Files:** `sa.yaml`, `role.yaml`, `rolebinding.yaml`

**Commands:**
```bash
kubectl apply -f sa.yaml -f role.yaml -f rolebinding.yaml
kubectl auth can-i get pods --as=system:serviceaccount:interview-app:app-sa -n interview-app
```

**Expected Output:** `yes`

---

### Drill 10: PVC
**Problem:** Create 1Gi PVC with ReadWriteOnce access mode

**Files:** `pvc.yaml`

**Commands:**
```bash
kubectl apply -f pvc.yaml
kubectl get pvc -n interview-app
```

**Expected Output:** PVC Bound (or Pending if no PV)

---

### Drill 11: CronJob
**Problem:** Create CronJob that runs `echo "Hello"` every 5 minutes

**Files:** `cronjob.yaml`

**Commands:**
```bash
kubectl apply -f cronjob.yaml
kubectl get cronjobs -n interview-app
```

**Expected Output:** CronJob with schedule `*/5 * * * *`

---

### Drill 12: NetworkPolicy
**Problem:** Allow ingress to nginx only from pods with label `role=frontend`

**Files:** `networkpolicy.yaml`

**Commands:**
```bash
kubectl apply -f networkpolicy.yaml
kubectl get networkpolicies -n interview-app
```

**Expected Output:** NetworkPolicy created

---

### Drill 13: HPA
**Problem:** Create HPA for nginx: min 2, max 5, target CPU 80%

**Files:** `hpa.yaml`

**Commands:**
```bash
kubectl apply -f hpa.yaml
kubectl get hpa -n interview-app
```

**Expected Output:** HPA targeting deployment

---

### Drill 14: OpenShift Route
**Problem:** Create Route for nginx service with TLS edge termination

**Files:** `route.yaml`

**Commands:**
```bash
oc apply -f route.yaml
oc get routes -n interview-app
```

**Expected Output:** Route with edge TLS

---

### Drill 15: Full Stack Debug
**Problem:** Given a failing deployment, debug and fix it

**Scenario:**
- Pod in CrashLoopBackOff
- Image: `nginx:wrong-tag`

**Steps:**
```bash
kubectl get pods -n interview-app
kubectl describe pod <name> -n interview-app
kubectl logs <pod-name> -n interview-app
# Find: ImagePullBackOff or wrong image
kubectl edit deployment nginx -n interview-app
# Fix image tag
kubectl rollout status deployment/nginx -n interview-app
```

**Expected Output:** Deployment running with correct image

---

## 8. 7-Day Practice Plan

### Day 1: Foundations (30 min)

| Time | Task |
|------|------|
| 10 min | Memorize Core YAML structure |
| 10 min | Write Namespace, Pod from memory |
| 10 min | Practice kubectl: get, describe, logs |

**Homework:** Write 3 Pod YAMLs with different configurations

---

### Day 2: Deployments & Services (35 min)

| Time | Task |
|------|------|
| 15 min | Write Deployment from memory |
| 10 min | Write ClusterIP, NodePort Services |
| 10 min | Practice: deploy nginx, expose, curl |

**Homework:** Deploy app, scale to 5 replicas, rollback

---

### Day 3: Config & Secrets (35 min)

| Time | Task |
|------|------|
| 10 min | Write ConfigMap from memory |
| 10 min | Write Secret from memory |
| 15 min | Mount ConfigMap/Secret in Pod |

**Homework:** Create app with env vars from ConfigMap and Secret

---

### Day 4: Networking & Storage (40 min)

| Time | Task |
|------|------|
| 15 min | Write Ingress from memory |
| 10 min | Write PVC from memory |
| 15 min | Practice PVC mount in deployment |

**Homework:** Deploy app with persistent storage and ingress

---

### Day 5: Advanced Resources (40 min)

| Time | Task |
|------|------|
| 10 min | Write ServiceAccount + Role |
| 10 min | Write RoleBinding |
| 10 min | Write Job and CronJob |
| 10 min | Practice RBAC: test with can-i |

**Homework:** Create SA with specific permissions, verify access

---

### Day 6: OpenShift & Production (45 min)

| Time | Task |
|------|------|
| 15 min | Practice oc commands |
| 15 min | Write Route from memory |
| 15 min | Add probes, resources, HPA |

**Homework:** Deploy production-ready app with all best practices

---

### Day 7: Full Review & Drills (45 min)

| Time | Task |
|------|------|
| 15 min | Complete Drills 1-5 |
| 15 min | Complete Drills 6-10 |
| 15 min | Debug scenarios |

**Final Test:** Deploy full stack from scratch in 15 minutes:
- Namespace
- Deployment with probes and resources
- Service
- Ingress
- ConfigMap and Secret

---

## Quick Reference Card

### Must-Know Commands

```bash
# Generate YAML
kubectl create deployment <name> --image=<img> --dry-run=client -o yaml
kubectl create service clusterip <name> --tcp=<port>:<target> --dry-run=client -o yaml
kubectl create configmap <name> --from-literal=<k>=<v> --dry-run=client -o yaml
kubectl create secret generic <name> --from-literal=<k>=<v> --dry-run=client -o yaml

# Debug
kubectl get pods -o wide
kubectl describe pod <name>
kubectl logs <pod> [-c container] [-f] [--previous]
kubectl exec -it <pod> -- /bin/sh
kubectl get events --sort-by=.metadata.creationTimestamp

# RBAC
kubectl auth can-i <verb> <resource> --as=system:serviceaccount:<ns>:<sa>
```

### Must-Know apiVersions

```
Pod/Service/ConfigMap/Secret/PVC/SA/Namespace → v1
Deployment/StatefulSet/DaemonSet/ReplicaSet → apps/v1
Job/CronJob → batch/v1
Ingress/NetworkPolicy → networking.k8s.io/v1
Role/RoleBinding → rbac.authorization.k8s.io/v1
HPA → autoscaling/v2
Route (OpenShift) → route.openshift.io/v1
```

### Label Matching Rules

```
Deployment selector.matchLabels  ═══╗
                                    ╠══► Must be IDENTICAL
Pod template.metadata.labels    ═══╝

Service selector  ════════════════════► Must match Pod labels
```

---

> **Good luck with your interview! Remember: Practice writing, not just reading.**
