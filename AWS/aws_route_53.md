# AWS Route 53

## Core Concepts & Components

*   **Domain names:** A domain is the name, such as example.com, that your users use to access your application.
*   **Hosted zones:** Specify how you want Route 53 to respond to DNS queries for a domain such as example.com.
*   **Health checks:** Monitor your applications and web resources, and direct DNS queries to healthy resources.
*   **Traffic flow:** Use a visual tool to create policies for multiple endpoints in complex configurations.
*   **Resolver:** Route DNS queries between your VPCs and your network.

## How It Works: Key Steps

*   **Domain Name Registration:** Register a domain and point it to AWS Route 53.
*   **Hosted Zone Creation:** Create a hosted zone to manage DNS records.
*   **DNS Records:** Add records (e.g., A, CNAME, MX) to route traffic to various endpoints.
*   **Routing Policies:** Set up routing policies based on your needs, such as latency-based or failover routing.
*   **Health Checks:** Configure health checks to monitor endpoints and trigger failover when needed.

## Common DNS Record Types

*   **A Record (IPv4):** Maps a domain name to an IPv4 address.
    *   Example: `www.google.com => 12.34.56.78`
*   **AAAA Record (IPv6):** Maps a domain name to an IPv6 address.
    *   Example: `www.example.com => 2001:db8::1`
*   **CNAME Record:** Maps a domain name to another domain name (alias).
    *   Example: `blog.example.com => www.example.com`
*   **MX Record:** Directs mail to an email server.
    *   Example: `example.com => mail.example.com (Priority 10)`
*   **TXT Record:** Provides text information to external sources for verification or configuration.
    *   Example: `example.com => "v=spf1 include:_spf.example.com ~all"`
*   **NS Record:** Specifies the authoritative name servers for the domain.
    *   Example: `example.com => ns-123.awsdns-45.org`
*   **SRV Record:** Specifies the location of services.
    *   Example: `_sip._tcp.example.com => 10 60 5060 sipserver.example.com`

## Common Use Cases

*   **Hosting Websites:** Manage domain names and route traffic to web applications.
*   **Load Balancing:** Distribute traffic across multiple endpoints using weighted or latency-based routing.
*   **Disaster Recovery:** Use health checks and failover routing for high availability.
*   **Multi-Region Deployments:** Route traffic to the closest region for low latency.

## Summary of Billing

*   **Billable Components** include hosted zones, DNS queries, health checks, domain registration, and traffic policies.
*   **Costs vary** based on usage and the type of configuration (e.g., standard vs. advanced routing policies).
*   **Free Tier:** Route 53 does not include a free tier, so charges start as soon as you use its services.


# Resources
* [AWS in ONE VIDEO 🔥 For Beginners 2025 [HINDI] | MPrashant](https://www.youtube.com/watch?v=N4sJj-SxX00)