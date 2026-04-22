# Google Cloud Computing Foundations Study Notes

*in progress*

Personal notes from the **Google Cloud Computing Foundations** certificate course. Each file covers one lab or topic written to be quick to read and easy to come back to when I forget how something works.

---

## Topics covered so far

### Cloud Shell & gcloud CLI
Basic resource management via terminal. Setting up regions and zones, environment variables, creating and SSHing into VMs, managing firewall rules, filtering output, and reading Cloud Logging logs.

### IAM & API Management
How Google Cloud handles identity and permissions. Projects and their identifiers, IAM roles (viewer / editor / owner), granting access, and enabling APIs through the console.

### App Engine - Serverless Computing
Running web apps without managing servers. Difference between Standard (sandbox, scales to zero) and Flexible (Docker, more control) environments. Deploying a Python Flask app using `gcloud app deploy`.

### Cloud Run & Event-driven Functions
Writing code that runs only when triggered by an event. Built a Node.js function triggered by a Pub/Sub message, deployed it with `gcloud functions deploy`, and tested it end-to-end through logs.

### Google Kubernetes Engine (GKE)
Running containerized apps across multiple machines. Covers the full cluster architecture - Master, Nodes, Pods, Deployments, Services and how `kubectl` communicates with the Kubernetes API. Deployed a containerized app, exposed it with a LoadBalancer, and cleaned up the cluster.
