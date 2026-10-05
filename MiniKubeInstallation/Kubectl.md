# Kubectl CLI Reference and Architecture

## 1. Overview and Architecture

`kubectl` (pronounced "cube-control" or "kube-c-t-l") is the official Kubernetes command-line client. It serves as the primary operational gateway between developers/operators and the Kubernetes control plane.

### How `kubectl` Interacts with the Cluster

When you issue any `kubectl` command, the following pipeline executes:
1. `kubectl` reads your configuration file located at `~/.kube/config`.
2. It identifies the target cluster API server address (e.g., `https://192.168.49.2:8443`), client certificates, and default namespace.
3. It converts your CLI commands or declarative YAML files into HTTP REST API requests.
4. The requests are sent to the `kube-apiserver`, where they undergo Authentication, Authorization (RBAC), and Admission Control.
5. If valid, the state is persisted into the `etcd` key-value store, and controllers reconcile the desired state.

---

## 2. Installation

`kubectl` is distributed as a self-contained static binary.

### Arch Linux

```bash
sudo pacman -S kubectl
```

### Ubuntu / Debian

```bash
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update
sudo apt install -y kubectl
```

### Manual Binary Installation (Linux x86-64)

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
rm kubectl
```

### Verification

```bash
kubectl version --client
```

---

## 3. Configuration and Context Management (`~/.kube/config`)

The `~/.kube/config` file (often referred to simply as the "kubeconfig") manages connections to multiple clusters through three structural objects:

* **Clusters:** The URL endpoints and Certificate Authorities (CAs) of target clusters.
* **Users:** Cryptographic credentials (client certificates, client keys, tokens).
* **Contexts:** Triplet bindings combining a **Cluster**, a **User**, and an optional target **Namespace**.

### Managing Contexts

```bash
# View active kubeconfig settings (omits sensitive raw credentials)
kubectl config view

# List all configured contexts
kubectl config get-contexts

# Identify currently active context
kubectl config current-context

# Switch active context to minikube
kubectl config use-context minikube

# Set default namespace for current context
kubectl config set-context --current --namespace=default
```

---

## 4. Fundamental Command Syntax

Every `kubectl` command follows a standardized syntax structure:

```bash
kubectl <action/verb> <resource_type/noun> <resource_name> [flags]
```

### Common Actions (Verbs)

| Action | Description | Example |
| :--- | :--- | :--- |
| `apply` | Applies declarative configuration from a manifest file or stdin. | `kubectl apply -f mypod.yaml` |
| `get` | Displays basic tabular status of one or more resources. | `kubectl get pods` |
| `describe` | Displays detailed status, metadata, conditions, and real-time event logs. | `kubectl describe pod mypod` |
| `logs` | Fetches terminal stdout/stderr logs emitted by a container. | `kubectl logs mypod` |
| `exec` | Executes a shell or single command inside a running container. | `kubectl exec -it mypod -- /bin/sh` |
| `delete` | Gracefully removes resources by name or file manifest. | `kubectl delete -f mypod.yaml` |
| `run` | Imperatively spins up a standalone Pod running a designated image. | `kubectl run mypod --image=nginx` |

---

## 5. Cluster Inspection Commands

To confirm cluster availability and node readiness:

### Checking Cluster Endpoints

```bash
kubectl cluster-info
```

Output confirms the location of the `kube-apiserver` and CoreDNS components.

### Inspecting Cluster Nodes

```bash
kubectl get nodes
```

Expected output:
```text
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   10m   v1.31.0
```

Add `-o wide` to inspect internal IP addresses, OS images, kernel versions, and container runtimes:

```bash
kubectl get nodes -o wide
```

---

## 6. Output Formatting Modifiers

The `-o` flag formats command output:

* `-o wide`: Includes extra informational columns (such as IP addresses and target nodes).
* `-o yaml`: Dumps the full server-side API representation in YAML format.
* `-o json`: Dumps the full server-side API representation in JSON format.
* `-o jsonpath='{...}'`: Extracts precise attributes using JSONPath expressions.

Example:
```bash
# Extract only the Pod IP address
kubectl get pod mypod -o jsonpath='{.status.podIP}'
```

---

## 7. Troubleshooting and Diagnostic Commands

### Issue 1: `The connection to the server localhost:8080 was refused`
* **Root Cause:** `kubectl` cannot find `~/.kube/config` and falls back to a default localhost port.
* **Resolution:** Ensure Minikube generated the configuration:
  ```bash
  minikube update-context
  ls -l ~/.kube/config
  ```

### Issue 2: `error: You must be logged in to the server (Unauthorized)`
* **Root Cause:** Client certificates expired or became corrupted.
* **Resolution:** Regenerate cluster certificates via Minikube:
  ```bash
  minikube update-context
  # If certificates remain corrupted:
  minikube stop && minikube start
  ```

### Issue 3: Client and Server Version Skew
* **Root Cause:** Kubernetes supports a maximum difference of +/- 1 minor version between `kubectl` and the `kube-apiserver`.
* **Resolution:** Check versions using `kubectl version` and align client version with cluster version.
