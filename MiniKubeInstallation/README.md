# Module 1: Minikube Installation and Setup

## Overview

This module covers the foundational steps required to prepare, configure, and verify a local Kubernetes development environment using Minikube and Docker.

Minikube allows students and engineers to run a full-featured Kubernetes control plane directly on a local laptop or workstation without requiring cloud infrastructure accounts.

---

## Module Contents

This module is organized into three sequential technical guides:

1. [Installation Guide](Installation.md):
   * Hardware requirements (CPU, RAM, Disk).
   * Hypervisor and container runtime configuration (Docker engine and non-root user setup).
   * Minikube binary installation across Linux distributions.
   * Initial cluster startup (`minikube start --driver=docker`).

2. [Configuration Guide](Configuration.md):
   * Hardware resource tuning (`--memory`, `--cpus`, `--disk-size`).
   * Permanent configuration defaults.
   * Cluster lifecycle commands (`status`, `pause`, `unpause`, `stop`, `delete`).
   * Context synchronization with `minikube update-context`.
   * Addons (Metrics Server, Ingress, Dashboard).
   * Cluster networking and service tunneling.

3. [Kubectl Guide](Kubectl.md):
   * Architecture and communication model with `kube-apiserver`.
   * Kubeconfig architecture (`~/.kube/config`), clusters, users, and contexts.
   * Essential CLI syntax and verbs (`get`, `describe`, `logs`, `exec`, `apply`, `delete`).
   * Output formatting flags (`-o wide`, `-o yaml`, `-o jsonpath`).
   * Diagnostics and troubleshooting.

---

## Key Learning Objectives

* Understand how container drivers (Docker) power single-node local Kubernetes clusters.
* Manage cluster state efficiently while preserving host computer resources.
* Configure `kubectl` to target and authenticate against the local Minikube control plane.
