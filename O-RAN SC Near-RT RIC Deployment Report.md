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

 **What worked:**  
- `kind create cluster --name ric` successfully created a local cluster.  
- Docker engine and WSL2 integration functioning properly.  


---

## 2. Deploying RIC

- Navigated to `ric-plt-ric-dep/bin`.
- Used `install` script to deploy the RIC platform into the `kind` cluster.

 **What worked:**  
- The `install` script deployed RIC services.  
- Pods were visible in `kubectl get pods -A`.  
- Basic RIC components (E2 Manager, Subscription Manager, A1 Mediator, etc.) came up.

**Command Executed**

```
 $ kubectl get pods -n ricplt 
```

**Response**

```
NAME                                                         READY   STATUS    RESTARTS       AGE
deployment-ricplt-a1mediator-67f8bcb5f7-gtnwt                1/1     Running   0              15h
deployment-ricplt-alarmmanager-86c79854b5-wsxxp              1/1     Running   0              15h
deployment-ricplt-appmgr-765dfc8d7d-kd9vc                    1/1     Running   0              15h
deployment-ricplt-e2mgr-7c4c5cddd6-4wwv5                     1/1     Running   0              15h
deployment-ricplt-e2term-alpha-9d966757b-7jjp8               1/1     Running   0              15h
deployment-ricplt-o1mediator-67f4ffb445-vpg2d                1/1     Running   0              15h
deployment-ricplt-rtmgr-54d89448df-gcw89                     1/1     Running   43 (64s ago)   15h
deployment-ricplt-submgr-7d86786bdd-kxr9n                    1/1     Running   0              15h
deployment-ricplt-vespamgr-846d876485-5r7tr                  1/1     Running   0              15h
r4-infrastructure-kong-6874569c56-sm2qt                      2/2     Running   0              15h
r4-infrastructure-prometheus-alertmanager-59c9f4bf85-xzr8l   2/2     Running   0              15h
r4-infrastructure-prometheus-server-8df8c44b-5fl58           1/1     Running   0              15h
statefulset-ricplt-dbaas-server-0                            1/1     Running   0              15ha
```

**Command Executed**

```
$ kubectl get svc -n ricplt
```

**Response**

```
NAME                                        TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                         AGE
aux-entry                                   ClusterIP      10.96.81.144    <none>        80/TCP,443/TCP                  15h
r4-infrastructure-kong-manager              NodePort       10.96.174.116   <none>        8002:31946/TCP,8445:30246/TCP   15h
r4-infrastructure-kong-proxy                LoadBalancer   10.96.245.42    <pending>     80:32080/TCP,443:32443/TCP      15h
r4-infrastructure-kong-validation-webhook   ClusterIP      10.96.77.188    <none>        443/TCP                         15h
r4-infrastructure-prometheus-alertmanager   ClusterIP      10.96.51.147    <none>        80/TCP                          15h
r4-infrastructure-prometheus-server         ClusterIP      10.96.182.232   <none>        80/TCP                          15h
service-ricplt-a1mediator-http              ClusterIP      10.96.26.238    <none>        10000/TCP                       15h
service-ricplt-a1mediator-rmr               ClusterIP      10.96.223.238   <none>        4561/TCP,4562/TCP               15h
service-ricplt-alarmmanager-http            ClusterIP      10.96.78.54     <none>        8080/TCP                        15h
service-ricplt-alarmmanager-rmr             ClusterIP      10.96.233.165   <none>        4560/TCP,4561/TCP               15h
service-ricplt-appmgr-http                  ClusterIP      10.96.82.147    <none>        8080/TCP                        15h
service-ricplt-appmgr-rmr                   ClusterIP      10.96.124.56    <none>        4561/TCP,4560/TCP               15h
service-ricplt-dbaas-tcp                    ClusterIP      None            <none>        6379/TCP                        15h
service-ricplt-e2mgr-http                   ClusterIP      10.96.118.172   <none>        3800/TCP                        15h
service-ricplt-e2mgr-rmr                    ClusterIP      10.96.80.34     <none>        4561/TCP,3801/TCP               15h
service-ricplt-e2term-prometheus-alpha      ClusterIP      10.96.10.98     <none>        8088/TCP                        15h
service-ricplt-e2term-rmr-alpha             ClusterIP      10.96.220.0     <none>        4561/TCP,38000/TCP              15h
service-ricplt-e2term-sctp-alpha            NodePort       10.96.122.10    <none>        36422:32222/SCTP                15h
service-ricplt-o1mediator-http              ClusterIP      10.96.184.20    <none>        9001/TCP,8080/TCP,3000/TCP      15h
service-ricplt-o1mediator-tcp-netconf       NodePort       10.96.9.56      <none>        830:30830/TCP                   15h
service-ricplt-rtmgr-http                   ClusterIP      10.96.32.213    <none>        3800/TCP                        15h
service-ricplt-rtmgr-rmr                    ClusterIP      10.96.215.163   <none>        4561/TCP,4560/TCP               15h
service-ricplt-submgr-http                  ClusterIP      None            <none>        3800/TCP                        15h
service-ricplt-submgr-rmr                   ClusterIP      None            <none>        4560/TCP,4561/TCP               15h
service-ricplt-vespamgr-http                ClusterIP      10.96.250.113   <none>        8080/TCP,9095/TCP               15h
```

---

## 3. Running an xApp 
At this stage, the RIC platform is running.  
The next steps will involve:
- Packaging an xApp.  
- Deploying it into the `ricxapp` namespace.  
- Verifying that it registers with the RIC components.


---

### xApp Onboarding Issue

**Command Executed**
```bash
xapp_onboarder onboard --config_file_path=CONFIG_FILE_PATH --shcema_file_path=SCHEMA_FILE_PATH
```
The above command was executed with correct file paths*
```
ler.artifacts_manager
2025-08-20 22:52:24,913 - INFO - >>>>> Starting development xapp_onboarder at http://None/api/v1/ <<<<< - xapp_onboarder.server.server
 * Serving Flask app "xapp_onboarder.server.server" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: on
2025-08-20 22:52:25,004 - INFO -  * Running on http://0.0.0.0:8888/ (Press CTRL+C to quit) - werkzeug
2025-08-20 22:52:25,017 - INFO -  * Restarting with stat - werkzeug
2025-08-20 22:52:29,172 - INFO - Artifact directory trimming thread started. - xapp_onboarder.helm_controller.artifacts_manager
2025-08-20 22:52:29,173 - INFO - >>>>> Starting development xapp_onboarder at http://None/api/v1/ <<<<< - xapp_onboarder.server.server
2025-08-20 22:52:29,243 - WARNING -  * Debugger is active! - werkzeug
2025-08-20 22:52:29,245 - INFO -  * Debugger PIN: 258-454-394 - werkzeug
2025-08-20 23:09:59,856 - INFO - 127.0.0.1 - - [20/Aug/2025 23:09:59] "GET /xapps HTTP/1.1" 404 - - werkzeug
2025-08-20 23:20:57,773 - INFO - 127.0.0.1 - - [20/Aug/2025 23:20:57] "GET /xapps HTTP/1.1" 404 - - werkzeug
```
**Observed Behavior**
- The command did not terminate and instead started serving a Flask app indefinitely on 127.0.0.1:8090.
- Expected behavior was for the onboarding process to complete and exit, not to run as a server.

### API CALL
**Troubleshooting Steps**
- Verified that the xapp_onboarder tool was running by checking health endpoint:
    ```
    curl http://127.0.0.1:8090/health
    ```
    Response:
    ```
    {"healthy": true}
    ```
- Cross-checked official O-RAN SC documentation — found that the onboard command is supposed to only perform onboarding, but in this case it instead started the Flask app.
- This led to the conclusion that the onboarding script was misconfigured (running in server mode instead of completing the onboarding task), which is the root cause of the indefinite execution.


Ran it again and it did some beep-beep-boop-boop.

**Request**
```
curl http://localhost:8888/api/v1/health
```
**Response**
```
{
    "error_source": "xapp_onboarder",
    "error_message": "Cannot connect to local helm repo.",
    "status": "Service not ready."
}
```
**Inference**
- The server is fine - the missing piece is the Helm chart repo where xApp lives.

Installed helm charts again.

## xApp Docker Image

I built and pushed the **KPM Basic xApp** Docker image.

- **Dockerfile** was used to containerize the xApp.
- The image was tagged and pushed to Docker Hub:

**Request**
  ```
  docker build -t <your-dockerhub-username>/kpm-basic-xapp:latest .
  docker push <your-dockerhub-username>/kpm-basic-xapp:latest
  ```
  **Response**
  ```
  The push refers to repository [docker.io/sved2805/kpm-basic-xapp]
b3ba8a31ba0b: Pushed
319bfd7bf52a: Pushed
4f4fb700ef54: Mounted from library/redis
302e3ee49805: Pushed
0e97c2bd1ea6: Pushed
ead58722cdcc: Pushed
030d7bdc20a6: Pushed
2c9c9f443f20: Pushed
b388af958c29: Pushed
d04201fc61e0: Pushed
a3f1dfe736c5: Pushed
3971691a3637: Pushed
019053a11dd8: Pushed
97fffc93c500: Pushed
0c5280fa8c66: Pushed
93767d5eb4e8: Pushed
e0fee55bfaa1: Pushed
cdcf3f638a13: Pushed
0.1.0: digest: sha256:302e1971954612b629e0689091f928e3f0d3ee74e595c935c3065bb5492cd768 size: 856  
```

## 4. Deploying E2 Sim
- Cloned the Repo.
- Installed dependencies
  ```
  sudo apt-get install -y build-essential git cmake libsctp-dev lksctp-tools autoconf automake libtool bison flex libboost-all-dev
  sudo apt-get clean

  ```

- Built the official e2sim
  ```
  cd ~/e2-interface/e2sim/
  mkdir build
  cd build
  cmake ..
  make package
  ```
Note: Since the xApp is not yet deployed, E2Sim cannot meaningfully stream data to it.
## 5. References

- [O-RAN SC RIC Deployment Repo](https://github.com/o-ran-sc/ric-plt-ric-dep)  
- [O-RAN SC xApp Framework](https://github.com/o-ran-sc/ric-plt-xapp-frame)  
- [Kind Documentation](https://kind.sigs.k8s.io/)  

---
