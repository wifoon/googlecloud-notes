A Cloud Run function is a piece of code that runs in response to an event, such as an HTTP request, a message from a messaging service, or a file upload.
  
They are **event-driven**, which means they run only when triggered, so you don’t need to keep a server running all the time.
  
Common use cases:
- Generate thumbnails after image upload to Cloud Storage
- Send notifications after Pub/Sub messages
- Process Firestore data automatically
- Run lightweight backend automation tasks

---  

### Create Function Project

Create working directory and files

```bash
mkdir gcf_hello_world && cd $_

# Initialize Node.js project files:
touch index.js package.json
nano index.js
```

We create a simple function that writes a message to the Cloud Run function logs.

``` js
const functions = require('@google-cloud/functions-framework');

// Register a CloudEvent callback with the Functions Framework that will
// be executed when the Pub/Sub trigger topic receives a message.
functions.cloudEvent('helloPubSub', cloudEvent => {
  // The Pub/Sub message is passed as the CloudEvent's data payload.
  const base64name = cloudEvent.data.message.data;

  const name = base64name
    ? Buffer.from(base64name, 'base64').toString()
    : 'World';

  console.log(`Hello, ${name}!`);
});
```

- `cloudEvent()` registers an event trigger
- Function listens for Pub/Sub messages
- Message content is decoded from Base64
- Output is written to logs

and dependencies

```bash
nano package.json
```

```json
{
  "name": "gcf_hello_world",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "dependencies": {
    "@google-cloud/functions-framework": "^3.0.0"
  }
}
```

Install packages:

```bash
npm install
```


### Deploying Function

Function is deployed with Pub/Sub topic trigger. Every new message sent to `cf-demo` automatically starts the function.

**Pub/Sub (Publisher / Subscriber)** is a messaging service.
One service sends a message to a **topic**, another service (function) receives it and reacts automatically.

Flow: `publish message -> Pub/Sub topic -> function executes`

```bash
gcloud functions deploy nodejs-pubsub-function \
  --gen2 \
  --runtime=nodejs_version \
  --region=REGION \
  --source=. \
  --entry-point=helloPubSub \
  --trigger-topic cf-demo \
  --stage-bucket PROJECT_ID-bucket \
  --service-account cloudfunctionsa@PROJECT_ID.iam.gserviceaccount.com \
  --allow-unauthenticated
```

- `--gen2` second generation Cloud Functions
- `--runtime` Node.js runtime version
- `--entry-point` exported function name
- `--trigger-topic` Pub/Sub trigger
- `--source` current folder source code

Now we can verify the deployment by following command

```bash
gcloud functions describe nodejs-pubsub-function \
  --region=REGION
```

If status shows `ACTIVE` the deployment succeeded.

### Testing Function

I tested the function by sending a Pub/Sub message:

```bash
gcloud pubsub topics publish cf-demo --message="Cloud Function Gen2"
```

Let's check the logs to see if we succeeded