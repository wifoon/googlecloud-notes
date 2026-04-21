
Google Cloud Resource Management via Terminal, basic uses of gcloud cli.

---
### Environment Configuration

The `gcloud` tool stores properties that determine the default behavior of commands.

List the active account name and project ID:

```bash
gcloud auth list
gcloud config list project
```


Setting the default projects region and zone:

```bash
# Setting region and zone
gcloud config set compute/region europe-west1
gcloud config set compute/zone europe-west1-b
```

```bash
# To view the project region and zone setting
gcloud config get-value compute/region
gcloud config get-value compute/zone
```


**Environment Variables**:
we can store project ID and zone in env variables to automate scripts and commands.

```bash
export PROJECT_ID=$(gcloud config get-value project)
export ZONE=$(gcloud config get-value compute/zone)
echo -e "PROJECT_ID: $PROJECT_ID\nZONE: $ZONE"
```

---

### Compute Instances
Management of Virtual Machine (VM) instances.

**Create instance VM**:

```bash
gcloud compute instances create gcelab2 --machine-type e2-medium --zone $ZONE
```

- `gcloud compute` allows you to manage your Compute Engine resources in a format that's simpler than the Compute Engine API.
- `instances create` creates a new instance.
- `gcelab2` is the name of the VM.
- The `--machine-type` flag specifies the machine type as _e2-medium_.
- The `--zone` flag specifies where the VM is created.

List the compute instance available in the project:

```bash
gcloud compute instances list
```

**SSH Connection**:

Once we have created the virtual machine, we can connect to it via SSH and install nginx web server.

```bash
# Connection and authorization
gcloud compute ssh gcelab2 --zone $ZONE 

# Instaling a web server after connecting
sudo apt update && sudo apt install -y nginx
```

---

### Networking & Firewall

Adding a rule to a firewall in a project and displaying existing rules.

```bash
# List the firewall rules in the project
gcloud compute firewall-rules list
```

Add the VM a tag to bind that tag to a firewall rule to open port 80 only for machines with that tag

```bash
gcloud compute instances add-tags gcelab2 --tags http-server,https-server
```


**Firewall Rules**:
Incoming traffic permission on port 80 for instances with `http-server` tag.

```bash
gcloud compute firewall-rules create default-allow-http \
        --direction=INGRESS --priority=1000 --network=default \
        --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 \
        --target-tags=http-server
```


Verify communication is possible for http to the virtual machine:

```bash
curl http://$(gcloud compute instances list --filter=name:gcelab2 --format='value(EXTERNAL_IP)')
```


---

### Filtering & Logging

Viewing logs is essential to understanding the working of project. Use `gcloud` to access the different logs available on Google Cloud.

**Filtering**:

Using the `--filter` flag to filter the output.
```bash
# Filtering the list of instances by name
gcloud compute instances list --filter="name=('gcelab2')"
# Filtering the list of rules by network and allow
gcloud compute firewall-rules list --filter="NETWORK:'default' AND ALLOW:'icmp'" 
```

**Cloud Logging**:
Reading system and audit logs.

```bash
gcloud logging logs list --filter="compute"
gcloud logging read "resource.type=gce_instance" --limit 5
gcloud logging read "resource.type=gce_instance AND labels.instance_name='gcelab2'" --limit 5
```
 
---

### SDK Components

CLI Component Management.

```bash
# Displaying installed tools (e.g. bq, gsutil)
gcloud components list
# Update CLI to latest version
gcloud components update
```