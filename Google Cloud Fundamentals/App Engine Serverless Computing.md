Google Cloud **App Engine** is a PaaS platform that allows developers to focus on code, completly isolating them from managing infrastructure.

#### Two main types of environments

- Standard Environment: Secure, isolated environment (sandbox). Scales automatically to zero when there is no traffic.
- Flexible Environment: Allows you to run Docker containers and custom libraries, offering more control over your infrastructure.

App Engine apps can easily communicate with other Google Cloud services such as NoSQL databases, storage etc.

---

First check the cloned repository locally

```bash
# Cloning the repository with the Python example
git clone https://github.com/GoogleCloudPlatform/python-docs-samples.git
cd python-docs-samples/appengine/standard_python3/hello_world

# Python Virtual Environment Configuration
sudo apt update && sudo apt install -y python3-venv
python3 -m venv myenv
source myenv/bin/activate
```

and we check the effect in the browser after changing port to 5000.

```bash
flask --app main run
```

![[Pasted image 20260422174919.png]]