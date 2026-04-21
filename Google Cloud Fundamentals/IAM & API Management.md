
Navigating the Google Cloud Console, managing Identity and Access Management (IAM), and handling Cloud APIs. Based on the Google Cloud Computing Foundations series.

![](../attachments/Pasted%20image%2020260421165922.png)

---
In Google Cloud, every resource must belong to a **Project**. Projects are isolated from each other and identified by:
* **Project Name**: A user-friendly name (can be changed).
* **Project ID**: A globally unique identifier (cannot be changed).
* **Project Number**: An automatically assigned unique number.

---
### IAM (Identity & Access Management)

IAM roles defines **Who** has **What** permissions on **Which** resource.

**Basic Roles (Primitive):**

| Role Name | Permission Level | Description |
| :--- | :--- | :--- |
| `roles/viewer` | Read-only | View resources without modifying state. |
| `roles/editor` | Read + Write | All viewer permissions plus the ability to modify/delete resources. |
| `roles/owner` | Full Access | All editor permissions plus managing roles, permissions, and billing. |

To grant access via Console:
`Navigation Menu` -> `IAM & Admin` -> `IAM` -> `Grant Access`.

![](../attachments/Pasted%20image%2020260421170333.png)
---
### APIs & Services

Most Google Cloud features are exposed via **APIs**. By default, many APIs are disabled to minimize security risks and costs.

**Enabling Dialogflow API**
The Dialogflow API allows building conversational interfaces (chatbots) using machine learning.

`APIs & Services` -> `Library`.