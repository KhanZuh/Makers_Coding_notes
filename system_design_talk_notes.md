# System Design Talk Notes
*Date: June 24, 2025*

## Talk Overview
**Theme:** Making system design as digestible as your sandwich
**Focus Areas:** Chaos Engineering, WebSockets, Microservices

---

## 🐒 Chaos Monkey & Chaos Engineering

### What is Chaos Monkey?
- **Origin:** Netflix's tool for randomly terminating services/instances in production
- **Purpose:** Test system resilience by deliberately introducing failures
- **Philosophy:** "Break things on purpose before they break on their own"

### Key Concepts
- **Chaos Engineering:** Discipline of experimenting on distributed systems
- **Blast Radius:** Limiting the scope of chaos experiments
- **Steady State:** Understanding normal system behavior before introducing chaos

### Benefits
- Discovers hidden failure modes
- Builds confidence in system resilience
- Reduces mean time to recovery (MTTR)
- Forces teams to build more robust systems

### Implementation Notes
- Start small with non-critical services
- Always have rollback plans
- Monitor everything during experiments
- Document learnings and improvements

---

## 🔌 WebSockets

### Fun Fact
WebSockets start as HTTP requests but "upgrade" mid-handshake to maintain persistent, full-duplex connections. It's like starting a formal letter and switching to texting!

### Technical Details
- **Protocol:** Upgrades from HTTP/HTTPS to WS/WSS
- **Handshake:** Uses HTTP Upgrade header
- **Connection:** Persistent, bidirectional communication
- **Use Cases:** Real-time chat, live updates, gaming, trading platforms

### Advantages
- Lower latency than polling
- Reduced server overhead
- Real-time bidirectional communication
- Works through firewalls and proxies

### Challenges
- Connection management at scale
- Handling disconnections gracefully
- Load balancing sticky sessions
- Memory usage for persistent connections

---

## 🏗️ Microservices Architecture

### What Are Microservices?
- Small, independent services that communicate over networks
- Each service owns its data and business logic
- Deployed and scaled independently

### How They Enabled Modern Tech Ecosystem

#### Before Microservices (Monoliths)
- Single deployable unit
- Shared database
- Technology lock-in
- Scaling challenges

#### After Microservices
- **Containerization:** Docker, container orchestration
- **Cloud Native:** Kubernetes, service meshes
- **DevOps Culture:** Independent team deployments
- **API Economy:** Services as building blocks
- **Polyglot Architecture:** Right tool for each job

### Key Benefits
- **Scalability:** Scale individual components
- **Team Autonomy:** Independent development and deployment
- **Technology Diversity:** Different languages/frameworks per service
- **Fault Isolation:** Failures don't cascade

### Challenges Introduced
- **Distributed System Complexity:** Network calls, latency
- **Data Consistency:** Eventual consistency, distributed transactions
- **Operational Overhead:** Monitoring, logging, tracing
- **Service Discovery:** How services find each other

---

## 🎯 Key Takeaways

### System Design Principles
1. **Design for Failure:** Assume components will fail
2. **Embrace Observability:** Monitor, log, trace everything
3. **Start Simple:** Don't over-engineer early
4. **Iterate and Learn:** Use feedback loops

### Modern Tech Stack Evolution

- Monolith → Microservices → Serverless/Edge
- Polling → WebSockets → Event Streams
- Manual Testing → Chaos Engineering → Continuous Resilience