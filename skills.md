You are my DevOps interview coding coach.

Goal:
Create ONE single Markdown file (README.md) that teaches me how to WRITE Kubernetes + OpenShift resources during interviews (not just explain concepts). I know how things work conceptually, but I struggle to remember exact YAML syntax and CLI commands.

Audience:
DevOps interview candidate. Needs fast recall and replication under pressure.

Output requirements (strict):
1) Output ONLY one Markdown file content (no extra commentary).
2) Use simple English.
3) Must be step-by-step, with "copy-paste templates" and "fill-in-the-blanks".
4) Include BOTH:
   - Kubernetes YAML + kubectl CLI
   - OpenShift YAML + oc CLI
5) For every resource:
   - Provide a minimal YAML template
   - Provide a production-ish YAML template (still interview-friendly)
   - Provide the CLI commands to create/apply, get, describe, logs, exec, delete
   - Provide 3 common mistakes + how to debug
   - Provide a memory trick (mnemonic / checklist)
6) Add a “Daily practice plan (7 days)” at the end with tasks.
7) Add a “Interview drill section” where you give me exercises and expected outputs.
8) Use lots of tables for fast revision and include internal hyperlinks (TOC with anchors).
9) No external links required.

Structure (must follow):
# Kubernetes + OpenShift Interview Coding Playbook
## 0. How to use this file in interview prep (10 minutes/day)
## 1. Core YAML rules you must memorize (indentation, apiVersion/kind/metadata/spec)
## 2. Kubectl essentials (most used commands)
## 3. Kubernetes Resources (WRITE THESE)
For each resource below follow the SAME sub-structure:
### <Resource Name>
- When to use (2 lines)
- Minimal YAML (template)
- Interview-ready YAML (template)
- kubectl commands (create/apply/get/describe/logs/exec/delete)
- Debug checklist
- Top mistakes (3)
- Memory trick (1)
Resources list (cover all):
A) Namespace
B) Pod
C) Deployment
D) ReplicaSet (only explain relationship; YAML optional)
E) Service (ClusterIP, NodePort, LoadBalancer)
F) ConfigMap
G) Secret (generic + tls secret)
H) Ingress (basic)
I) ServiceAccount
J) RBAC: Role + RoleBinding
K) Resource Requests/Limits
L) Liveness/Readiness Probes
M) Volumes: emptyDir, hostPath (warn), PVC
N) PVC + PV (simple)
O) StatefulSet (simple)
P) DaemonSet (simple)
Q) Job + CronJob
R) HPA (simple)
S) NetworkPolicy (basic allow)
T) Helm basics: values + template skeleton (interview-level)
## 4. OpenShift Resources + oc CLI (WRITE THESE)
Explain differences and give YAML + commands:
A) Project (vs namespace)
B) DeploymentConfig (legacy) vs Deployment (recommended)
C) Route (most important)
D) BuildConfig + ImageStream (basic)
E) SecurityContextConstraints (concept + what to do in interview if blocked)
F) oc commands: new-app, new-build, start-build, rollout, expose, logs, rsh
## 5. Fast “copy templates” section (1 page)
Provide ready-to-copy minimal templates for:
- Deployment + Service + ConfigMap + Secret + Ingress
- OpenShift Deployment + Service + Route
## 6. Debugging Playbook (Interview Friendly)
- Pod not starting (CrashLoopBackOff)
- ImagePullBackOff
- Service not reachable
- Ingress/Route issues
- RBAC forbidden errors
Commands + what to look for.
## 7. Interview Drills (practice like coding round)
Give 15 drills with increasing difficulty.
Each drill must include:
- Problem statement
- What files to write (YAML names)
- Exact commands to run
- What output I should see (short)
## 8. 7-Day practice plan
Give day-wise plan with 30-45 mins/day.

Extra requirements:
- Use consistent placeholders: <app-name>, <namespace>, <image>, <port>, <config-key>
- Show YAML with comments explaining important lines.
- Keep templates short but correct.
- Prefer kubectl apply -f and oc apply -f style.
- Include both imperative and declarative methods where relevant.
- Include “Interview tips” callout boxes in Markdown.

Now generate the README.md.