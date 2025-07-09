# Networking Fundamentals Reference Sheet
***Essential concepts for Containerization, Deployment, and CI/CD***
## Core Networking Concepts
### Virtual Connections & Packet Switching

**Virtual Connection**: Simulated connection that appears direct but uses packet switching

**Packet Switching**: Communication broken into small data packets with destination addresses

**Why it matters for containers**: Container networking is entirely virtual - containers communicate through virtual networks

## Ports & Interfaces

- **Port**: Numbered "socket" on a computer (e.g., port 3000, 5432, 80)
- **Interface**: Hardware that sends/receives packets
- **Container relevance**: Port mapping (-p 3000:3000) exposes container services (DOCKER)
    > When you run a container, it operates in an isolated network environment. By default, services inside the container can't be reached from outside. Port mapping creates a bridge between your host machine and the container, forwarding traffic from a host port to a container port. Basic syntax:  ```docker run -p <host_port>:<container_port> <image>```

## Networking Tools & Commands

### ping - Basic Connectivity
```
ping google.com
ping 192.168.1.1
```
> What it does: Tests if a computer/server is reachable 

> Container use: Verify container can reach database, external APIs, other services
> ```bash
> # From inside a container
>ping database-server
>ping db.example.com
>```

**Test container-to-container communication:**

```bash
# From one container to another
ping web-app
ping microservice-auth
```
**Check if container can reach host:**

```bash
ping host.docker.internal  # Docker Desktop
ping gateway.docker.internal
```
***Ping is your first diagnostic tool when things aren't connecting properly. It quickly tells you if the basic network path exists before you dig into more complex issues like ports, protocols, or application-level problems.***

### What this means for cloud: 
***When you see "health checks" in your AWS console, they're essentially automated ping tests checking if your servers are responding.***

## traceroute - Network Path Discovery
```bash
traceroute google.com
# Shows each "hop" between you and destination
```

### The Network Hierarchy:

```
Your Computer
    ↓
Home Router (192.168.x.x)
    ↓
ISP Local Network
    ↓
ISP Regional Network
    ↓
Internet Backbone
    ↓
Destination ISP
    ↓
Target Computer
```

**What it does**: Shows the path packets take through the network

**Deployment use**: Troubleshoot connectivity issues between services

### What this means for cloud: 
> Those "Availability Zones" and "Regions" in AWS? They're physically separate network locations. Understanding network hops helps you optimize for latency.

## telnet - How We Formed Many Connections Over One Cable

## The Problem: 
We needed multiple programs to use the same network connection simultaneously.
## What telnet does: 
Creates a virtual terminal connection to another computer over the network.
```
telnet towel.blinkenlights.nl
# (This famously plays Star Wars in ASCII art!)
```
## The Innovation - Multiplexing:
```
Single Physical Connection
├── Connection 1 (Port 23 - Telnet)
├── Connection 2 (Port 80 - Web)
├── Connection 3 (Port 25 - Email)
└── Connection 4 (Port 22 - SSH)
```
### Key Concept 
***Ports***: Think of ports like apartment numbers. The IP address gets you to the building, the port number gets you to the specific apartment (service).

```
Common Ports:
- 22: SSH (secure shell)
- 80: HTTP (web)
- 443: HTTPS (secure web)
- 25: SMTP (email)
- 3306: MySQL database
```
### What this means for cloud: 
> Those "Security Groups" in AWS? They're controlling which ports are open. 
> When you see "Port 80" in your security rules, now you know what that means.

## curl - How We Developed Common Languages
**The Problem**: Different applications needed standardized ways to communicate.
 
**What curl does**: Speaks HTTP (and other protocols) to fetch data from web servers.

```bash
curl -v http://example.com
# GET / HTTP/1.1
# Host: example.com
# User-Agent: curl/7.64.1
# Accept: */*
# 
# HTTP/1.1 200 OK
# Content-Type: text/html
# Content-Length: 1256
```

```bash
REQUEST:
Method + Path + HTTP Version
Headers (key: value pairs)
Body (optional)

RESPONSE:
Status Code + HTTP Version
Headers (key: value pairs)
Body (the actual content)
```

### REST APIs evolved from this:

```bash
# GET - retrieve data
curl https://api.example.com/users/123

# POST - create new data
curl -X POST -d '{"name":"John"}' https://api.example.com/users

# PUT - update data
curl -X PUT -d '{"name":"Jane"}' https://api.example.com/users/123

# DELETE - remove data
curl -X DELETE https://api.example.com/users/123
```

### What this means for cloud: 
Every API call you make to AWS, every microservice communication, every "webhook" - they're all using HTTP protocols that curl helped standardize.

## dig - How We Developed Human-Readable Names
### The Problem:
Remembering IP addresses like 142.250.191.14 is impossible for humans.
### What dig does: 
Looks up domain names in the DNS (Domain Name System) to find IP addresses.
```bash
dig google.com
# google.com.    300    IN    A    142.250.191.14
```

### The DNS Hierarchy:
```bash
Root Servers (.)
    ↓
Top Level Domain (.com, .org, .uk)
    ↓
Domain (google.com)
    ↓
Subdomain (mail.google.com)
```
### Different DNS Record Types:
```bash
dig google.com A      # IPv4 address
dig google.com AAAA   # IPv6 address
dig google.com MX     # Mail servers
dig google.com NS     # Name servers
dig google.com CNAME  # Canonical name (alias)
```
### What this means for cloud: 
> When you set up a "Load Balancer" in AWS and give it a domain name, you're creating DNS records. 

> Route53 is AWS's DNS service.

## mkcert - How We Secured the Internet

### The Problem: 
Anyone could intercept and read network traffic.

### What mkcert does: 
Creates SSL/TLS certificates for secure HTTPS connections.

```bash
mkcert localhost
# Created a new certificate valid for the following names:
# - "localhost"
# The certificate is at "./localhost.pem"
# The key file is at "./localhost-key.pem"
```

### How HTTPS Works:
```
1. Client connects to server
2. Server sends its certificate (public key)
3. Client verifies certificate with Certificate Authority
4. Client generates session key, encrypts it with server's public key
5. Server decrypts session key with its private key
6. Both use session key for encrypted communication
```
### The Certificate Chain:
```
Root CA (Certificate Authority)
    ↓ (signs)
Intermediate CA
    ↓ (signs)
Your Website Certificate
```

### What this means for cloud: 
> Those "Certificate Manager" services in AWS? They're automating the process of getting and renewing SSL certificates. 

> Load balancers terminate SSL connections - they decrypt incoming traffic and re-encrypt it for backend servers.


## docker - How We Created the Cloud

### The Problem: 
Applications needed consistent environments across different computers.
### What docker does: 
Packages applications with all their dependencies into portable containers.

```bash
# Create a container
docker run -p 8080:80 nginx
# This runs nginx web server, mapping port 8080 on your machine to port 80 in the container
```

### The Container Concept:

```
Traditional VM:
Hardware → OS → Hypervisor → [Guest OS → App][Guest OS → App]

Containers:
Hardware → OS → Container Runtime → [Container][Container]
```

### Docker Networking:
```bash
# Create a network
docker network create myapp-network

# Run containers on the same network
docker run --network myapp-network --name database postgres
docker run --network myapp-network --name web -p 8080:80 nginx
```

### What this means for cloud:

- ECS/EKS: AWS services that run Docker containers
- Lambda: Serverless functions that run in containers behind the scenes
- Fargate: Run containers without managing servers
- ECR: Store your Docker images

## Putting It All Together: Understanding Your Cloud Control Panel

### AWS console:

1. Instance ID: A unique identifier for your virtual machine
2. Public IPv4 address: The IP address others can reach your server at (remember ping?)
3. Private IPv4 address: The internal network address (remember traceroute and network hierarchy?)
4. Security Groups: Rules controlling which ports are open (remember telnet and ports?)
5. Instance Type: The size/power of your virtual machine
6. Availability Zone: Which physical data center your server is in (remember network topology?)

### A Simple CI/CD Pipeline Understanding

basic deployment
```
1. Code pushed to GitHub
2. GitHub webhook (HTTP POST) triggers build
3. Build process creates Docker container
4. Container pushed to registry (using HTTPS)
5. Load balancer (with SSL certificate) routes traffic
6. DNS points domain name to load balancer
7. Health checks (ping) monitor application
8. Logs show HTTP requests (like curl output)
```