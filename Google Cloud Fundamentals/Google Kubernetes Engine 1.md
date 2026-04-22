Google Cloud **Kubernetes Engine (GKE)** lets you run containerized apps across multiple machines without manually managing the infrastructure. Under the hood it uses **Kubernetes** an open source system that takes care of where your containers run, restarts them when they crash, and scales them when traffic grows.

#### Two types of machines in every cluster

- **Master (Control Plane)** decides where to run your containers, watches the cluster state, reacts to failures. Google manages this for you, you never touch it.
- **Nodes** regular VMs (Compute Engine) where your containers actually run. You pay for these.


---

### Environment Setup

```bash
gcloud config set compute/region REGION
gcloud config set compute/zone ZONE
```

---

### Create a GKE Cluster

> Cluster names must start with a letter, end with an alphanumeric, and cannot exceed 40 characters.

```bash
gcloud container clusters create --machine-type=e2-medium --zone=ZONE lab-cluster
```

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

**Create a Deployment** — Kubernetes pulls the image and starts running it on your nodes:

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