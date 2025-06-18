# AWS Lambda

AWS Lambda is a serverless computing service that lets you run code in response to events without managing servers.

You just upload your code, and AWS automatically handles the rest, scaling as needed and only charging for the time your 
code runs.

## Event-Driven Execution

* Lambda is an event-driven service, meaning that it runs your code in response to certain triggers or events.
* These events can come from many different AWS services like:
    * S3 (file uploads),
    * DynamoDB (database changes),
    * API Gateway (HTTP requests),
    * CloudWatch (scheduled events), etc.

## Automatic Scaling

* AWS Lambda automatically scales the execution of functions in response to the number of incoming requests.
* If a thousand requests come in at the same time, AWS Lambda will handle them in parallel, making it highly scalable
  without any configuration.

## Pay-as-You-Go

* Lambda uses a pay-as-you-go pricing model. You are billed based on the number of function executions and the duration
  of each function's runtime (measured in milliseconds).
* This is cost-effective as you only pay for the compute time that you use, and there's no charge for idle time.

## Key Considerations & Limitations

* **Execution Time Limit:** Lambda functions can only run for a maximum of 15 minutes. If you need longer-running tasks, 
  Lambda might not be the best choice.
* **Stateless:** Lambda functions don't keep state between invocations, so they're best for tasks that don't require 
  long-term memory.
* **Cold Start Delays:** If a Lambda function hasn't run in a while, there's sometimes a slight delay—called a 'cold 
  start'—when it starts up. This can add a little latency, but AWS provides ways to mitigate it for critical functions.

## Common Use Cases

* **Image Processing:** Let's say users upload images to your app. You can set up Lambda to automatically resize,
  compress, or even apply filters to each image as it's uploaded.
* **Data Transformation:** If you need to clean up or process data before storing it in a database, a Lambda function
  can handle that transformation automatically.
* **Real-Time Notifications:** If an event happens—like a new user signing up—you can use Lambda to trigger an email,
  SMS, or other notifications instantly.



# Resources
* [AWS in ONE VIDEO 🔥 For Beginners 2025 [HINDI] | MPrashant](https://www.youtube.com/watch?v=N4sJj-SxX00)
