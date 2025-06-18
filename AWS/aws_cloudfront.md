# AWS CloudFront Complete Guide

## What is AWS CloudFront?
**AWS CloudFront** is a **Content Delivery Network (CDN)** that speeds up the delivery of web content to users by
caching it at servers (**edge locations**) close to them, improving load times and performance globally.

## What does CloudFront Cache?

AWS CloudFront primarily caches static content like **images, CSS, JavaScript, and videos**. It can also cache dynamic
content (e.g., HTML or API responses) if configured with caching policies and headers.

By default, sensitive or user-specific data and backend logic are not cached. Cache behavior is controlled via TTLs,
cache behaviors, and origin headers.

## CloudFront Infrastructure

Amazon CloudFront has three types of infrastructure to securely deliver content with high performance to end users:

* **CloudFront Regional Edge Caches (RECs)** are situated within AWS Regions, between your applications' web server and
  CloudFront Points of Presence (POPs) and embedded Points of Presence. CloudFront has 13 RECs globally.
* **CloudFront Points of Presence** are situated within the AWS network and peer with internet service provider (ISP)
  networks. CloudFront has 600+ POPs in 100+ cities across 50+ countries.
* **CloudFront embedded Points of Presence** are situated within internet service provider (ISP) networks, closest to
  end viewers. In addition to CloudFront POPs, there are 600+ embedded POPs across 200+ cities in North America, Europe,
  and Asia.

### Without CloudFront
```
EC2 instance  Amazon S3  
       \      /
        \    /
         \  /
          \/
       End User
```

### With CloudFront
```
                                                               |-------------------> Viewers  
                                   |-----------------| Edge Location---------------> Viewers 
                                   |                           |-------------------> Viewers
       |--------------| Regional Edge Cache (REC)                                         Europe    
       |                           |                           |-------------------> Viewers  
       |                           |-----------------| Edge Location---------------> Viewers
Amazon CloudFront                                              |-------------------> Viewers
       |                           
       |                                                       |-------------------> Viewers 
       |                           |-----------------| Edge Location---------------> Viewers 
       |                           |                           |-------------------> Viewers
       |---------------| Regional Edge Cache (REC)                                        Asia
                                   |                           |-------------------> Viewers
                                   |-----------------| Edge Location---------------> Viewers
                                                               |-------------------> Viewers
```

We can also use amazon route53 with latency regional routing to route traffic to the nearest edge location. But that is
not that much provide low latency as compared to CloudFront.

## Browser Caching

Browsers act like a mini-CDN by caching website files (like images, CSS, and JavaScript) locally on a user's device, which speeds up loading for repeat visits.

Only helps individual users.

## Included in Always Free Tier

-   1 TB of data transfer out to the internet per month
-   10,000,000 HTTP or HTTPS Requests per month
-   2,000,000 CloudFront Function invocations per month
-   2,000,000 CloudFront KeyValueStore reads per month
-   Free SSL certificates
-   No limitations, all features available

## Key Differences: CloudFront vs. Multi-Location Hosting

| Feature               | CloudFront                                | Multi-Location Hosting                             |
|-----------------------|-------------------------------------------|----------------------------------------------------|
| **Performance**       | Optimized for static content and caching. | Optimized for dynamic content near users.          |
| **Cost**              | Pay-per-use, often cheaper.               | Higher costs for server and database setup.        |
| **Ease of Use**       | Easy to set up, minimal management.       | Requires managing multiple server instances.       |
| **Scalability**       | Auto-scales globally.                     | Requires manual scaling per location.              |
| **Content Freshness** | Cached content may require invalidation.  | Dynamic content is always current.                 |
| **Compliance**        | Less control over data residency.         | Full control over where data is hosted.            |

## Example: Setting up CloudFront with S3

### Step 1: Create S3 Bucket
Goes to s3 and click on "Create bucket", from general configuration, keep bucket type as "General purpose" and give the
bucket name as `jahid-my-cloudfront-bucket`.

Keep object ownership as "ACLs disabled".

Keep checked "Block all public access".

Keep bucket version as "Disable".

From default encryption, keep encryption type as "Server-side encryption with Amazon S3 managed keys (SSE-S3)" and keep
bucket key as "Enable".

Now click on "Create bucket" button.

### Step 2: Upload Content
From s3 dashboard, select `jahid-my-cloudfront-bucket` bucket and click on "Upload" button and select all the files
inside the static folder of the project and click on "Upload" button.

### Step 3: Create CloudFront Distribution
Now goes to CloudFront dashboard and click on "Create distribution" button.

At first "Get started" section, enter name of the distribution as `my-cloudfront-distribution`, keep already selected
"Single website or app" and click next button.

At second "Specify origin" select amazon s3 for origin type. At origin section s3 origin > click on browse s3 button and
select `jahid-my-cloudfront-bucket` bucket. Skip origin path. At settings part keep all origin settings like checked
"Grant CloudFront permission to access my origin", origin settings keep selected Use recommended origin settings and
click on next.

At third "Enable security", for Web Application Firewall (WAF) selecting "Do not enable security protections" as we are
just testing it and click on next.

At fourth state just review all and click on "Create distribution" button.

### Step 4: Configure Default Root Object
After those steps, site was not visible was getting assess denied message at the Distribution domain name, so I goes to
CloudFront > Distributions clicked on `my-cloudfront-distribution`and from "General" tab, click on "Edit" button from
Settings section and at "Default root object - optional" type `index.html` and click on "Save changes" button after that
it was working fine.

Now from the CloudFront > Distributions, click on `my-cloudfront-distribution` and copy the "Distribution domain name"
and paste it in the browser, it will show the site.

### Auto-Generated Bucket Policy
CloudFront add this policy to the bucket automatically when we create the distribution, so we don't need to add it:

```json
{
    "Version": "2008-10-17",
    "Id": "PolicyForCloudFrontPrivateContent",
    "Statement": [
        {
            "Sid": "AllowCloudFrontServicePrincipal",
            "Effect": "Allow",
            "Principal": {
                "Service": "cloudfront.amazonaws.com"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::jahid-my-cloudfront-bucket/*",
            "Condition": {
                "ArnLike": {
                    "AWS:SourceArn": "arn:aws:cloudfront::004407157540:distribution/E2XF6CRV3PAS51"
                }
            }
        }
    ]
}
```

## Resources
* [AWS in ONE VIDEO 🔥 For Beginners 2025 [HINDI] | MPrashant](https://www.youtube.com/watch?v=N4sJj-SxX00)