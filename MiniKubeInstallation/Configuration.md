# Minikube Cluster Configuration and Management

## 1. Overview

Once Minikube is installed, tailoring its resource parameters, managing cluster states, enabling supplementary features (addons), and managing Kubernetes contexts are required for daily development workflows.

This guide details configuration techniques, cluster lifecycle operations, context synchronization (`minikube update-context`), and troubleshooting strategies.

---

## 2. Resource Allocation and Defaults

By default, Minikube automatically detects and allocates a baseline amount of CPU and RAM from the host machine. You can explicitly customize resource allocations per cluster run or set permanent global defaults.

### Ephemeral Configuration Flags (Single Startup)

```bash
minikube start --driver=docker --cpus=4 --memory=4096m --disk-size=30g
```

* `--cpus <number>`: Number of virtual CPUs assigned to the Minikube node.
* `--memory <size-in-mb>`: Amount of system RAM reserved for the node (e.g., `4096m` or `4g`).
* `--disk-size <size>`: Maximum storage volume for container images and volumes (e.g., `30g`).

### Persistent Configuration Defaults

To avoid retyping flags on every restart, store preferences in Minikube's global settings:

```bash
# Set Docker as default driver
minikube config set driver docker

# Set default memory and CPU values
minikube config set memory 4096
minikube config set cpus 4

# View all saved configuration properties
minikube config view
```

---

## 3. Kubernetes Context and Synchronization

Minikube creates and registers a context named `minikube` inside `~/.kube/config`. The Kubernetes CLI (`kubectl`) uses this context to route API calls to the local Minikube control plane.

### The `minikube update-context` Command

```bash
minikube update-context
```

#### What does it do?
`minikube update-context` updates the cluster endpoint in your local `kubeconfig` to ensure it reflects the active IP address and control plane port assigned to the Minikube container or virtual machine. It also switches the current active `kubectl` context to `minikube`.

#### When is it needed?
* **Host Reboot or Daemon Restart:** If Docker restarts or reassigns container IP addresses (e.g., changing from `192.168.49.2` to another subnet address), your existing `kubeconfig` will point to a stale IP address.
* **Context Switching:** If you have been working with a cloud cluster (e.g., EKS, GKE, AKS) or a secondary local cluster, running `minikube update-context` re-aligns your active workspace to Minikube without having to manually modify YAML configs.

Expected output:
```text
"minikube" context has been updated to point to 192.168.49.2:8443
Current context is "minikube"
```

---

## 4. Managing Cluster Lifecycle

To preserve host resources when not actively developing, control cluster execution states:

| Command | Action | Host Impact |
| :--- | :--- | :--- |
| `minikube status` | Inspects state of host, kubelet, apiserver, and kubeconfig. | Read-only check. |
| `minikube pause` | Pauses Kubernetes namespaces without shutting down containers. | Halts CPU consumption instantly. |
| `minikube unpause` | Resumes paused cluster operations. | Restores CPU state. |
| `minikube stop` | Gracefully terminates control plane and runtime container/VM. | Frees RAM and CPU completely; state saved on disk. |
| `minikube start` | Starts stopped cluster or boots a fresh one if absent. | Reallocates RAM and CPU. |
| `minikube delete` | Purges cluster VM/container, all workloads, images, and config. | Frees all disk space. |

---

## 5. Working with Minikube Addons

Minikube features an extensible addon architecture allowing built-in deployment of essential Kubernetes services.

### Listing Available Addons

```bash
minikube addons list
```

### Essential Addons for Development

* **Metrics Server** (enables `kubectl top nodes` and `kubectl top pods`):
  ```bash
  minikube addons enable metrics-server
  ```
* **Ingress Controller** (deploys NGINX Ingress Controller for layer 7 routing):
  ```bash
  minikube addons enable ingress
  ```
* **Kubernetes Dashboard** (launches web-based UI):
  ```bash
  minikube dashboard
  ```

---

## 6. Accessing Services and Networking

Because the Docker driver runs the Kubernetes node inside an isolated container bridge network (typically `192.168.49.x`), services of type `NodePort` or `LoadBalancer` cannot always be reached via `localhost` directly.

### Identifying Cluster IP

```bash
minikube ip
```

### Tunneling LoadBalancer Services

If your workload exposes a Service with `type: LoadBalancer`, start a tunnel route in a separate terminal:

```bash
minikube tunnel
```

`minikube tunnel` creates a network route to the cluster using your host network adapter, assigning an external IP reachable directly from your workstation.

---

## 7. Common Complications and Troubleshooting

### Issue 1: Connection Refused to 192.168.49.2:8443
* **Root Cause:** The Minikube container was stopped, or Docker reset its virtual interface.
* **Resolution:**
  ```bash
  minikube status
  minikube start
  minikube update-context
  ```

### Issue 2: Context Mismatch (`The connection to the server localhost:8080 was refused`)
* **Root Cause:** `kubectl` is attempting to connect to the default unauthenticated fallback URL because `~/.kube/config` is missing or the current context is unassigned.
* **Resolution:**
  ```bash
  minikube update-context
  kubectl config current-context
  ```

### Issue 3: Insufficient Storage Inside the Minikube Node
* **Root Cause:** Cached container images (`kicbase`, pulled workloads) filled the virtual disk.
* **Resolution:**
  ```bash
  minikube ssh -- docker system prune -af
  # Or purge and recreate with higher capacity:
  minikube delete
  minikube start --disk-size=40g
  ```
