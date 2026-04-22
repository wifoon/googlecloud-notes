Google Cloud Serverless Functions triggered by events such as Pub/Sub messages, HTTP requests, or file uploads.
  
**Cloud Run Functions** are small pieces of code executed only when a specific event happens.
  
They are **event-driven**, which means they run only when triggered, so you don’t need to keep a server running all the time.
  
Common use cases:
- Generate thumbnails after image upload to Cloud Storage
- Send notifications after Pub/Sub messages
- Process Firestore data automatically
- Run lightweight backend automation tasks

---  

#### Create Function Project

Create working directory

```bash
mkdir gcf_hello_world && 
```