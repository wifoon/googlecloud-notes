Google Cloud **Kubernetes Engine (GKE)** is a managed environment for deploying, managing, and scaling containerized applications using Google infrastructure. GKE clusters are powered by the **Kubernetes** open source cluster management system.

Key platform benefits:

- Load balancing for Compute Engine instances
- Automatic scaling and automatic node software upgrades
- Node auto-repair to maintain node health and availability
- Logging and monitoring with Cloud Monitoring

---
### Create a GKE Cluster

A cluster consists of at least one **master machine** and multiple **worker nodes** (Compute Engine VM instances).

> Cluster names must start with a letter, end with an alphanumeric, and cannot exceed 40 characters.

```bash
gcloud container clusters create --machine-type=e2-medium --zone=ZONE lab-cluster
```

Expected output:

```
NAME: lab-cluster
LOCATION: ZONE
MACHINE_TYPE: e2-medium
NUM_NODES: 3
STATUS: RUNNING
```

---

### Authenticate with the Cluster

After creation, obtain authentication credentials to interact with the cluster:

```bash
gcloud container clusters get-credentials lab-cluster
```

This generates a `kubeconfig` entry for the cluster, enabling `kubectl` commands.

---

### Deploy an Application

GKE uses **Kubernetes objects** to manage cluster resources:

- **Deployment** — manages stateless applications (e.g. web servers)
- **Service** — defines access rules and load balancing for external traffic

**Create a Deployment** from a container image:

```bash
kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0
```

- `--image` specifies the container image to deploy
- If no version tag is given, the latest version is pulled

**Expose the Deployment** as a Kubernetes Service with a load balancer:

```bash
kubectl expose deployment hello-server --type=LoadBalancer --port 8080
```

- `--port` — the port the container exposes
- `--type=LoadBalancer` — provisions a Compute Engine load balancer

**Verify the Service** and retrieve the external IP:

```bash
kubectl get service
```

```
NAME           TYPE          CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
hello-server   LoadBalancer  10.39.244.36   35.202.234.26  8080:31991/TCP   65s
```

> It may take a minute for the `EXTERNAL-IP` to be assigned. Re-run the command if it shows `pending`.

The application is then accessible in the browser at:

```
http://[EXTERNAL-IP]:8080
```

---

### Delete the Cluster

To avoid ongoing charges, delete the cluster when finished:

```bash
gcloud container clusters delete lab-cluster
```

Confirm with `Y` when prompted. Deletion may take a few minutes.