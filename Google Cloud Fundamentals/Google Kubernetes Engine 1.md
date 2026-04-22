Google Cloud **Kubernetes Engine (GKE)** lets you run containerized apps across multiple machines without manually managing the infrastructure. Under the hood it uses **Kubernetes** an open source system that takes care of where your containers run, restarts them when they crash, and scales them when traffic grows.


- **`kubectl`** manages what runs *inside* the cluster (Pods, Deployments, Services).

kubectl talks to the **Kubernetes API** running on the master. It knows where to send requests because `gcloud container clusters get-credentials` saves the cluster address and auth token to `~/.kube/config`, after that every `kubectl` command just works. 


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

Before you can run `kubectl` commands you need to point it at your cluster:

```bash
gcloud container clusters get-credentials lab-cluster
```

This writes a `kubeconfig` entry so `kubectl` knows how to talk to your cluster.

---

### Deploy an Application

Create a Deployment from a container image:

```bash
kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0
```

**Expose it as a Service** — creates a public IP and a load balancer in front of your Pods:

```bash
kubectl expose deployment hello-server --type=LoadBalancer --port 8080
```

**Check the Service** to get the external IP:

```bash
kubectl get service
```

```
NAME           TYPE          CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
hello-server   LoadBalancer  10.39.244.36   35.202.234.26  8080:31991/TCP   65s
```

> `EXTERNAL-IP` may show `pending` for a minute — just re-run the command.

App is live at `http://[EXTERNAL-IP]:8080`.

---

### Delete the Cluster

```bash
gcloud container clusters delete lab-cluster
```

Confirm with `Y`. Takes a few minutes.