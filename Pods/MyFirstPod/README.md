# My First Pod Laboratory

## Overview

This directory contains the hands-on materials, configuration manifests, execution guides, and terminal session screenshots for deploying and debugging your first Kubernetes Pod.

---

## Files in this Directory

* **[`mypod.yaml`](mypod.yaml):** The declarative Kubernetes Pod manifest defining an NGINX container workload.
* **[`commands.md`](commands.md):** Complete step-by-step hands-on laboratory execution guide, including detailed terminal screenshot analyses, output breakdowns, and troubleshooting recipes.
* **[`Screenshots/`](Screenshots/):** Terminal execution screenshots documenting every step of the laboratory:
  * `FirstCommands.png`: Updating context, deploying the manifest, and verifying status transitions.
  * `PodDescription.png`: Detailed resource inspection and event stream via `kubectl describe`.
  * `Logs.png`: Container standard output logs via `kubectl logs`.
  * `DeletePod.png`: Graceful workload termination and verification via `kubectl delete`.

---

## Quick Start

```bash
# 1. Update Minikube context
minikube update-context

# 2. Deploy the pod manifest
kubectl apply -f mypod.yaml

# 3. Check status
kubectl get pods -o wide

# 4. Inspect details and events
kubectl describe pod mypod

# 5. Review logs
kubectl logs mypod

# 6. Delete the pod
kubectl delete pod mypod
```

For full instructions, detailed analysis, and troubleshooting, read [`commands.md`](commands.md).
