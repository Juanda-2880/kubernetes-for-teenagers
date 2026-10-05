# Kubernetes for Teenagers: Study and Laboratory Guide

![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)
![Minikube](https://img.shields.io/badge/minikube-%232496ED.svg?style=for-the-badge&logo=kubernetes&logoColor=white)
![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Arch Linux](https://img.shields.io/badge/Arch%20Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white)
![YAML](https://img.shields.io/badge/yaml-%23ffffff.svg?style=for-the-badge&logo=yaml&logoColor=black)
![Bash](https://img.shields.io/badge/bash-%23121011.svg?style=for-the-badge&logo=gnu-bash&logoColor=white)
![Nginx](https://img.shields.io/badge/nginx-%23009639.svg?style=for-the-badge&logo=nginx&logoColor=white)

---

## 1. Project Purpose and Overview

This repository serves as a comprehensive study and review guide for the **Plataformas II** university course, developed from instructional materials and readings based on the *Kubernetes for Teenagers* literature and curriculum provided by the instructor.

The primary objective of this repository is to systematically organize, explain, and document every stage of working with Kubernetes:
* Transitioning from local single-node cluster bootstrapping to deploying real-world containerized workloads.
* Deconstructing the purpose, operational rationale, and syntax behind every `minikube` and `kubectl` command.
* Analyzing manifest specifications line by line to understand how the Kubernetes control plane interprets configurations.
* Identifying real-world complications, error states, and troubleshooting methods encountered in everyday container operations.
* Documenting practical terminal executions with verified screenshot evidence.

---

## 2. Repository Structure and Navigation

The repository is modularized into distinct, self-contained sections representing each milestone of the learning progression:

```text
kubernetes-for-teenagers/
├── README.md                                  # Repository overview, architecture, and navigation
├── MiniKubeInstallation/                      # Module 1: Cluster Setup & Client Tooling
│   ├── README.md                              # Module 1 index and overview
│   ├── Installation.md                        # Prerequisites, Docker daemon, and Minikube setup
│   ├── Configuration.md                       # Resource allocation, lifecycle, context sync, addons
│   └── Kubectl.md                             # CLI client architecture, kubeconfig, and command reference
└── Pods/                                      # Module 2: Workload Primitives & Pod Lifecycle
    ├── README.md                              # Module 2 index and overview
    ├── What its a Pod?.md                     # Conceptual deep dive into Pods, networking, and patterns
    ├── ExplainPodConfig.md                    # Line-by-line breakdown of YAML manifests and best practices
    └── MyFirstPod/                            # Practical Hands-On Laboratory
        ├── README.md                          # Quick start and directory reference
        ├── mypod.yaml                         # Declarative NGINX Pod manifest
        ├── commands.md                        # Step-by-step execution guide with screenshots & debugging
        └── Screenshots/                       # Terminal session captures
            ├── FirstCommands.png              # Context update, pod creation, and wide status query
            ├── PodDescription.png             # Full pod inspection and chronological event stream
            ├── Logs.png                       # Standard output runtime logs from NGINX entrypoint
            └── DeletePod.png                  # Graceful pod termination and verification
```

---

## 3. Learning Modules Summary

### Module 1: Minikube Installation and Environment Setup
* **[Installation Guide](MiniKubeInstallation/Installation.md):** Step-by-step installation instructions for Linux (Arch, Ubuntu/Debian, Fedora), hypervisor/driver configurations (Docker engine), user permissions (`usermod -aG docker`), and verification commands.
* **[Configuration Guide](MiniKubeInstallation/Configuration.md):** Customizing cluster CPU and memory resources, setting global defaults, managing cluster states (`start`, `pause`, `stop`, `delete`), updating `kubectl` contexts with `minikube update-context`, enabling addons, and handling networking/tunnels.
* **[Kubectl Architecture & Reference](MiniKubeInstallation/Kubectl.md):** In-depth exploration of the official Kubernetes CLI tool, kubeconfig structure (`~/.kube/config`), context management, standard syntax patterns (`kubectl <verb> <noun>`), output formatting, and cluster validation.

### Module 2: Kubernetes Pods
* **[What is a Pod?](Pods/What%20its%20a%20Pod%3F.md):** The core atomic unit of Kubernetes orchestration. Details why Kubernetes utilizes Pods over standalone containers, shared network namespaces (`localhost`), shared volumes, multi-container design patterns (Sidecar, Adapter, Ambassador), lifecycle phases, restart policies, and limitations of raw Pods.
* **[Pod Manifest Configuration Analysis](Pods/ExplainPodConfig.md):** Structural analysis of `mypod.yaml`. Examines top-level keys (`apiVersion`, `kind`, `metadata`, `spec`), RFC 1123 naming rules, implicit container defaults (image tags and pull policies), expanded production configurations (ports, environment variables, resource limits), and dry-run validation.
* **[Hands-On Lab: My First Pod](Pods/MyFirstPod/commands.md):** Practical execution lab demonstrating deployment, inspection, real-time logging, interactive command execution, and workload teardown. Integrates visual terminal proof for every milestone.

---

## 4. Visual Laboratory Proofs

The practical exercises in this repository are verified with terminal captures:

| Stage | Visual Evidence | Key Concepts Demonstrated |
| :--- | :--- | :--- |
| **Context & Deployment** | [FirstCommands.png](Pods/MyFirstPod/Screenshots/FirstCommands.png) | `minikube update-context`, `kubectl apply -f mypod.yaml`, transition from `ContainerCreating` to `Running`, internal overlay IP assignment. |
| **Deep Diagnostic Inspection** | [PodDescription.png](Pods/MyFirstPod/Screenshots/PodDescription.png) | `kubectl describe pod mypod`, service account projection, container states, node binding, and chronological event sequence. |
| **Runtime Application Logs** | [Logs.png](Pods/MyFirstPod/Screenshots/Logs.png) | `kubectl logs mypod`, container entrypoint initialization (`/docker-entrypoint.sh`), worker process spawning. |
| **Workload Teardown** | [DeletePod.png](Pods/MyFirstPod/Screenshots/DeletePod.png) | `kubectl delete pod mypod`, graceful termination signals, verifying cluster cleanup with `kubectl get pods`. |

---

## 5. Summary of Common Complications and Solutions

| Component | Common Issue | Cause | Recommended Solution |
| :--- | :--- | :--- | :--- |
| **Minikube** | `DRV_DOCKER_NOT_RUNNING` | Docker daemon is stopped. | Run `sudo systemctl start docker`. |
| **Minikube** | Docker permission denied | User not part of `docker` group. | Run `sudo usermod -aG docker $USER` and `newgrp docker`. |
| **Kubectl** | Context endpoint unreachable | Host IP changed after reboot. | Run `minikube update-context`. |
| **Pod** | `ImagePullBackOff` | Typo in image name or private image without credentials. | Inspect `kubectl describe pod` events; correct image name in manifest. |
| **Pod** | `CrashLoopBackOff` | Application terminates immediately or encounters a fatal crash. | Run `kubectl logs mypod --previous` to inspect crash traces. |
| **Pod** | Stuck in `Pending` | Insufficient CPU/memory resources on node. | Verify `kubectl describe pod` events; increase Minikube hardware allocations. |
| **Networking** | Cannot curl Pod IP from host | Pod IP (`10.244.0.3`) is isolated inside cluster overlay network. | Use `kubectl port-forward pod/mypod 8080:80` to bridge traffic to `localhost`. |
| **Lifecycle** | Pod not self-healing | Standalone Pods lack controllers. | Use Deployments (`kind: Deployment`) for production resilience. |

---

## 6. Prerequisites

To replicate this laboratory environment locally, ensure you have:
* **Operating System:** Linux (Arch Linux, Ubuntu, Debian, or Fedora), macOS, or Windows with WSL2.
* **Hardware:** Minimum 2 CPU cores, 4 GB RAM, and 20 GB free disk space.
* **Software:**
  * Docker Engine (`>= 24.0`)
  * Minikube (`>= 1.30`)
  * Kubectl (`>= 1.30`)