# Minikube Installation Guide

## 1. Overview and Purpose

Minikube is an open-source tool that sets up a single-node or multi-node Kubernetes cluster inside a Virtual Machine (VM) or container on your local machine. It provides a lightweight, sandboxed environment tailored for learning Kubernetes concepts, developing containerized applications, and testing manifests locally without incurring cloud provider costs.

This guide outlines the system prerequisites, driver requirements, installation steps, and common troubleshooting scenarios encountered during initial setup.

---

## 2. Hardware and Software Prerequisites

Before installing Minikube, ensure your host system satisfies the following baseline requirements:

* **CPUs:** 2 or more physical or virtual CPU cores.
* **RAM:** 2 GB of available free memory (4 GB or more recommended).
* **Disk Space:** 20 GB of available disk storage.
* **Internet Connection:** Stable internet access to download Kubernetes container images and binaries.
* **Container Runtime / Hypervisor:** A container runtime or hypervisor must be installed and active:
  * **Docker** (recommended for Linux and macOS environments)
  * **Podman**
  * **KVM2** / **QEMU** (Linux hypervisors)
  * **VirtualBox** / **Hyper-V** (Windows / macOS legacy)

### Verifying Hardware Virtualization (Linux)

If using a hypervisor (such as KVM2 or VirtualBox), hardware virtualization must be enabled in your system BIOS/UEFI. Verify this by running:

```bash
grep -E --color 'vmx|svm' /proc/cpuinfo
```

If the command produces highlighted output (`vmx` for Intel or `svm` for AMD), hardware virtualization is supported and active.

---

## 3. Installing the Container Driver (Docker)

Minikube requires a driver to spin up the cluster environment. The Docker driver runs the Kubernetes cluster components inside a single Docker container, avoiding the heavy overhead of traditional VMs.

### Arch Linux (Pacman)

```bash
sudo pacman -Syu docker
sudo systemctl enable --now docker
```

### Ubuntu / Debian (APT)

```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg
# Install Docker Engine following official distribution repositories
sudo systemctl enable --now docker
```

### Fedora / RHEL (DNF)

```bash
sudo dnf install -y docker-ce docker-ce-cli containerd.io
sudo systemctl enable --now docker
```

### Managing Docker as a Non-Root User

By default, the Docker daemon binds to a Unix socket owned by `root`. Running Minikube under `sudo` is strictly discouraged because it can corrupt file permissions in `~/.minikube` and `~/.kube`. Add your user to the `docker` group:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

Verify that you can execute Docker without elevated permissions:

```bash
docker ps
```

---

## 4. Installing Minikube

### Method A: Direct Binary Download (Linux x86-64)

Download and install the official standalone Minikube binary:

```bash
# Download the binary
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

# Install the binary to /usr/local/bin
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Clean up the downloaded artifact
rm minikube-linux-amd64
```

### Method B: Native Package Managers

* **Arch Linux:**
  ```bash
  sudo pacman -S minikube
  ```
* **Homebrew (Linux / macOS):**
  ```bash
  brew install minikube
  ```
* **Windows (PowerShell as Administrator via Chocolatey or Winget):**
  ```powershell
  winget install Kubernetes.minikube
  ```

---

## 5. Verifying the Installation

Ensure the binary is recognized in your system `$PATH` and check the installed release:

```bash
minikube version
```

Expected output format:
```text
minikube version: v1.34.0
commit: ...
```

---

## 6. Starting Your First Minikube Cluster

Initialize the local cluster using the Docker driver:

```bash
minikube start --driver=docker
```

During execution, Minikube performs the following automated phases:
1. Validates host machine specifications and Docker daemon health.
2. Pulls the base Minikube node image (`kicbase`).
3. Creates a Docker container acting as a Kubernetes node.
4. Generates cluster certificates and cryptographic tokens.
5. Starts the control plane components (`kube-apiserver`, `etcd`, `kube-controller-manager`, `kube-scheduler`).
6. Configures the local `~/.kube/config` file to connect to the new cluster.

To verify cluster status immediately after initialization:

```bash
minikube status
```

---

## 7. Common Pitfalls and Troubleshooting

### Issue 1: Docker Daemon is Not Running
* **Error Message:** `Exiting due to DRV_DOCKER_NOT_RUNNING: Docker is not running`
* **Root Cause:** The `docker.service` systemd unit is stopped or failed.
* **Resolution:**
  ```bash
  sudo systemctl start docker
  sudo systemctl status docker
  ```

### Issue 2: Permission Denied Accessing Docker Socket
* **Error Message:** `permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock`
* **Root Cause:** Current active user is not in the `docker` user group or group membership has not refreshed.
* **Resolution:**
  ```bash
  sudo usermod -aG docker $USER
  newgrp docker
  # If issues persist, log out of your desktop session and log back in.
  ```

### Issue 3: Virtualization Disabled in BIOS
* **Error Message:** `VT-x/AMD-v hardware virtualization is disabled` (when using VM drivers like KVM or VirtualBox).
* **Root Cause:** CPU virtualization flags disabled in BIOS/UEFI settings.
* **Resolution:** Reboot host computer, enter BIOS/UEFI setup (typically F2, F10, or DEL), locate CPU Configuration, and enable Intel VT-x or AMD-V. Alternatively, switch to the `--driver=docker` driver which does not require nested hypervisors.

### Issue 4: Insufficient Disk or Memory Resources
* **Error Message:** `Requested memory allocation (2048MB) is not available` or `No space left on device`
* **Root Cause:** Host system has high memory usage or root partition is full.
* **Resolution:** Free system RAM by closing resource-heavy desktop applications or customize Minikube's allocation explicitly:
  ```bash
  minikube start --driver=docker --memory=2048m --cpus=2
  ```
