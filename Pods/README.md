# Module 2: Kubernetes Pods

## Overview

This module covers the core workload primitive of Kubernetes: the **Pod**. It addresses theoretical concepts, structural syntax analysis of YAML manifests, hands-on lifecycle management, and real-world troubleshooting techniques.

---

## Module Contents

1. [What is a Pod?](What%20its%20a%20Pod%3F.md):
   * Conceptual definition of the Pod as the atomic schedulable unit in Kubernetes.
   * Why Kubernetes wraps containers into Pods (shared network namespace, shared storage, co-scheduling).
   * Single-container vs. multi-container design patterns (Sidecar, Adapter, Ambassador).
   * Lifecycle phases (`Pending`, `Running`, `Succeeded`, `Failed`, `Unknown`).
   * Restart policies (`Always`, `OnFailure`, `Never`).
   * Practical complications with standalone Pods (lack of self-healing, port collisions, IP churn, immutability).

2. [Explaining Pod Configuration](ExplainPodConfig.md):
   * Deep dive into Kubernetes manifest architecture (`apiVersion`, `kind`, `metadata`, `spec`).
   * Line-by-line breakdown of `mypod.yaml`.
   * Defaults for image tags and pull policies.
   * Expanded production configurations (ports, environment variables, resource requests, and limits).
   * YAML syntax rules and client-side dry-run validation (`kubectl apply --dry-run=client`).

3. [Hands-On Lab: My First Pod](MyFirstPod/commands.md):
   * Practical lab walkthrough deploying `mypod.yaml`.
   * Verified command outputs with terminal screenshots.
   * Detailed inspections using `kubectl describe`, `kubectl logs`, and `kubectl exec`.
   * Declarative (`kubectl apply`) vs. imperative (`kubectl run`) deployment patterns.
   * Common complications: `ImagePullBackOff`, `CrashLoopBackOff`, stuck `Pending`, and host port tunneling.

---

## Key Learning Objectives

* Differentiate between bare container runtimes and Kubernetes Pod abstractions.
* Author and validate RFC 1123 compliant YAML manifests.
* Confidently deploy, inspect, debug, and tear down Pods using `kubectl`.
