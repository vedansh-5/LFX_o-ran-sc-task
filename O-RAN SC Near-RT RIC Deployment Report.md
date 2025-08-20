# O-RAN SC RIC Deployment & xApp Exploration

This document tracks my journey of setting up the O-RAN SC RIC platform and preparing for xApp development/testing. It includes the steps I’ve taken, what worked, and what didn’t.

---

## 1. Prerequisites & Setup

- Installed **Docker Desktop** (Windows, with WSL2 backend).
- Installed **kubectl**, **kind**, **helm**, **make**, **openssl**.
- Verified versions:
  - Docker working correctly.
  - `kind` cluster creation successful.
- Did **not** enable Kubernetes inside Docker Desktop (since `kind` manages its own cluster).

✅ **What worked:**  
- `kind create cluster --name ric` successfully created a local cluster.  
- Docker engine and WSL2 integration functioning properly.  

❌ **What didn’t work initially:**  
- Tried running `kind` before Docker was installed → failed with *"docker not found"*. Fixed after installing Docker.

---

## 2. Deploying RIC

- Navigated to `ric-plt-ric-dep/bin`.
- Used `install` script to deploy the RIC platform into the `kind` cluster.

✅ **What worked:**  
- The `install` script deployed RIC services.  
- Pods were visible in `kubectl get pods -A`.  
- Basic RIC components (E2 Manager, Subscription Manager, A1 Mediator, etc.) came up.

❌ **What didn’t work initially:**  
- Confusion about whether Kubernetes needed to be enabled in Docker Desktop (it doesn’t).  
- Some pods took longer to start, requiring patience.  

---

## 3. Running an xApp (next step)

At this stage, the RIC platform is running.  
The next steps will involve:  
- Packaging an xApp.  
- Deploying it into the `ricxapp` namespace.  
- Verifying that it registers with the RIC components.

(*Haven’t run an xApp yet — this is the next milestone.*)

---

## 4. Notes for Improvement

- Document exact versions of all dependencies.  
- Capture logs from failed attempts.  
- Add architecture diagram of RIC + xApp once deployment works fully.  

---

## 5. References

- [O-RAN SC RIC Deployment Repo](https://github.com/o-ran-sc/ric-plt-ric-dep)  
- [O-RAN SC xApp Framework](https://github.com/o-ran-sc/ric-plt-xapp-frame)  
- [Kind Documentation](https://kind.sigs.k8s.io/)  

---
