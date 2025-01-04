To handle and store **console logs** and **crash errors** for free or with minimal cost, you can use various approaches depending on your requirements for storage, analysis, and monitoring. Here are the recommended solutions:

---

### **1. Use File Logging with Winston**
Store logs locally in files using the **Winston** library. This is a lightweight solution and ideal for development or small-scale applications.

#### Install Winston:
```bash
npm install winston
```

#### Setup Winston Logger:
```typescript
import winston from "winston";

const logger = winston.createLogger({
  level: "info", // Logging level (e.g., 'info', 'error')
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.json()
  ),
  transports: [
    new winston.transports.File({ filename: "logs/errors.log", level: "error" }), // Store errors
    new winston.transports.File({ filename: "logs/combined.log" }), // Store all logs
  ],
});

// Add console logging in development
if (process.env.NODE_ENV !== "production") {
  logger.add(
    new winston.transports.Console({
      format: winston.format.simple(),
    })
  );
}

export default logger;
```

#### Example Usage:
```typescript
import logger from "./logger";

try {
  // Your logic here
  throw new Error("Simulated crash");
} catch (error) {
  logger.error("An error occurred:", error);
}
```

#### Result:
- **`logs/errors.log`**: Contains all error logs.
- **`logs/combined.log`**: Contains all logs (info, debug, error, etc.).

---

### **2. Cloud-Based Free Logging Solutions**
For a more scalable and centralized solution, consider the following free or low-cost services:

#### **a. Logtail (Free Tier Available)**
- Provides structured logging and real-time monitoring.
- Free tier includes up to 50MB of logs per month.

##### Setup:
1. Sign up at [Logtail](https://logtail.com/).
2. Get your ingestion token.
3. Install the **`winston-logtail`** transport:
   ```bash
   npm install @logtail/node winston-logtail
   ```

4. Add Logtail to Winston:
   ```typescript
   import { Logtail } from "@logtail/node";
   import { LogtailTransport } from "winston-logtail";
   import winston from "winston";

   const logtail = new Logtail("your-logtail-ingestion-token");

   const logger = winston.createLogger({
     level: "info",
     transports: [
       new LogtailTransport(logtail), // Logtail transport
       new winston.transports.Console(),
     ],
   });

   export default logger;
   ```

#### **b. Logz.io (Free Tier Available)**
- Open-source logging platform with 1GB/month free storage.
- Integrates with `winston` using the `winston-logzio` transport.

#### **c. Elastic Cloud (Free Tier)**
- Elastic Stack offers free tiers with a limited amount of log storage.
- You can integrate it with `winston` using the `winston-elasticsearch` transport.

---

### **3. Use a Crash Monitoring Tool**
Use a tool specifically designed for crash monitoring and error reporting.

#### **a. Sentry (Free Tier)**
- Tracks application crashes and logs error details.
- Free tier includes up to 5,000 events/month.

##### Setup:
1. Sign up at [Sentry](https://sentry.io/).
2. Install the Sentry SDK:
   ```bash
   npm install @sentry/node
   ```

3. Initialize Sentry in your application:
   ```typescript
   import * as Sentry from "@sentry/node";

   Sentry.init({
     dsn: "your-sentry-dsn", // Get this from Sentry dashboard
     tracesSampleRate: 1.0, // Adjust sampling for performance monitoring
   });

   // Example: Capturing errors
   try {
     throw new Error("Simulated crash");
   } catch (error) {
     Sentry.captureException(error);
   }
   ```

---

### **4. Stream Logs to Free Storage Services**
Stream your logs to a free cloud storage service like AWS S3 (Free Tier), Google Cloud Storage (Free Tier), or Azure Blob Storage.

#### Example with AWS S3:
1. **Set up AWS S3 Bucket**:
   - Create a bucket in AWS S3.
   - Note the bucket name and region.

2. **Install AWS SDK**:
   ```bash
   npm install aws-sdk
   ```

3. **Log to S3**:
   ```typescript
   import AWS from "aws-sdk";

   const s3 = new AWS.S3({
     region: "your-region",
     accessKeyId: "your-access-key-id",
     secretAccessKey: "your-secret-access-key",
   });

   const logToS3 = async (logMessage: string) => {
     const params = {
       Bucket: "your-bucket-name",
       Key: `logs/${Date.now()}.log`, // Log filename
       Body: logMessage,
     };

     await s3.upload(params).promise();
     console.log("Log uploaded to S3");
   };

   logToS3("This is a test log");
   ```

---

### **5. Use Node.js Process Handlers for Crashes**
Catch uncaught exceptions and unhandled promise rejections to log crash errors.

```typescript
import logger from "./logger";

process.on("uncaughtException", (error) => {
  logger.error("Uncaught Exception:", error);
  process.exit(1); // Exit to avoid unstable state
});

process.on("unhandledRejection", (reason) => {
  logger.error("Unhandled Rejection:", reason);
});
```

---

### Summary of Free Solutions:
1. **Local File Storage**: Use `winston` for file logging.
2. **Cloud Logging**: Logtail, Logz.io, or Elastic Cloud for free logging with analytics.
3. **Crash Monitoring**: Sentry for error tracking.
4. **Cloud Storage**: Stream logs to AWS S3, Google Cloud Storage, or Azure Blob Storage.

Choose based on your application's scale, requirements, and whether you need centralized log management. Let me know if you need help setting up any of these!
