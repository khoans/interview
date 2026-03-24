# 100 System Design Concepts for Software Engineer Interviews

## Questions 1-10: Fundamentals

**1.** What is scalability? Explain the difference between horizontal and vertical scaling.

**2.** What is a load balancer? How does it distribute traffic across servers?

**3.** What is caching? When should you use it and what are the common pitfalls?

**4.** What is a CDN (Content Delivery Network)? How does it improve performance?

**5.** What is the CAP theorem? Explain with real-world examples.

**6.** What is the difference between latency and throughput? How do you optimize each?

**7.** What is a reverse proxy? How does it differ from a forward proxy?

**8.** What is DNS and how does DNS resolution work?

**9.** What are the differences between TCP and UDP? When would you use each?

**10.** What is the difference between monolithic and microservices architecture?

## Questions 11-20: Database Concepts

**11.** What is database sharding? When and why would you shard a database?

**12.** What is database replication? Explain master-slave vs master-master replication.

**13.** What is the difference between SQL and NoSQL databases? When would you choose each?

**14.** What is database indexing? How does a B-tree index work?

**15.** What are ACID properties? Why are they important for transactions?

**16.** What is eventual consistency? How does it differ from strong consistency?

**17.** What is database partitioning? Explain horizontal vs vertical partitioning.

**18.** What is a write-ahead log (WAL)? How does it ensure data durability?

**19.** What is connection pooling? Why is it important for database performance?

**20.** What is the N+1 query problem? How do you solve it?

## Questions 21-30: Caching & Storage

**21.** Explain cache eviction policies: LRU, LFU, FIFO. When would you use each?

**22.** What is a write-through cache vs write-back cache vs write-around cache?

**23.** What is cache invalidation? Why is it called one of the two hard problems in CS?

**24.** What is Redis? How is it different from Memcached?

**25.** What is a distributed cache? How does consistent hashing help?

**26.** What is object storage (e.g., S3)? How does it differ from block storage and file storage?

**27.** What is a hot spot in caching? How do you handle a celebrity problem (thundering herd)?

**28.** What is cache-aside (lazy loading) pattern? When would you use read-through instead?

**29.** What is a bloom filter? How can it reduce unnecessary database lookups?

**30.** What is data lake vs data warehouse? When would you use each?

## Questions 31-40: Communication Patterns

**31.** What is REST? What are its constraints and how does it differ from RPC?

**32.** What is gRPC? When would you choose it over REST?

**33.** What is GraphQL? What problems does it solve compared to REST?

**34.** What are WebSockets? When would you use them instead of HTTP polling?

**35.** What is a message queue (e.g., SQS, RabbitMQ)? How does it decouple services?

**36.** What is an event-driven architecture? How does pub/sub differ from point-to-point messaging?

**37.** What is Apache Kafka? How does it differ from traditional message queues?

**38.** What is long polling vs short polling vs Server-Sent Events (SSE)?

**39.** What is an API gateway? What responsibilities does it handle?

**40.** What is the difference between synchronous and asynchronous communication in microservices?

## Questions 41-50: Microservices & Distributed Systems

**41.** What is service discovery? How do services find each other in a microservices architecture?

**42.** What is the circuit breaker pattern? How does it prevent cascading failures?

**43.** What is the saga pattern? How does it handle distributed transactions?

**44.** What is the sidecar pattern? How is it used in service meshes?

**45.** What is the strangler fig pattern? How do you migrate from monolith to microservices?

**46.** What is the bulkhead pattern? How does it isolate failures?

**47.** What is idempotency? Why is it critical in distributed systems?

**48.** What is a distributed lock? How do you implement it (Redis, Zookeeper)?

**49.** What is the two generals problem? How does it relate to distributed consensus?

**50.** What is leader election? How do algorithms like Raft and Paxos work at a high level?

## Questions 51-60: Reliability & Fault Tolerance

**51.** What is high availability? How do you calculate availability (nines)?

**52.** What is redundancy? Explain active-passive vs active-active configurations.

**53.** What is a single point of failure (SPOF)? How do you eliminate them?

**54.** What is failover? Explain cold, warm, and hot standby.

**55.** What is rate limiting? What algorithms exist (token bucket, leaky bucket, sliding window)?

**56.** What is backpressure? How do you handle it when a producer is faster than a consumer?

**57.** What is a retry strategy? Explain exponential backoff with jitter.

**58.** What is a health check? How do liveness and readiness probes differ?

**59.** What is chaos engineering? How does it improve system reliability?

**60.** What is graceful degradation? How does it differ from fail-fast?

## Questions 61-70: Security & Networking

**61.** What is OAuth 2.0? Explain the authorization code flow.

**62.** What is JWT (JSON Web Token)? What are its pros and cons vs session-based auth?

**63.** What is HTTPS/TLS? How does the TLS handshake work?

**64.** What is a firewall? Explain the difference between WAF and network firewall.

**65.** What is DDoS protection? What strategies exist at different layers?

**66.** What is CORS? Why does it exist and how do you configure it?

**67.** What is API rate limiting and throttling? How do you implement it at scale?

**68.** What is encryption at rest vs encryption in transit?

**69.** What is a VPC (Virtual Private Cloud)? How do you design network isolation?

**70.** What is zero-trust architecture? How does it differ from perimeter-based security?

## Questions 71-80: Data Processing & Analytics

**71.** What is batch processing vs stream processing? When would you use each?

**72.** What is MapReduce? How does it process large datasets in parallel?

**73.** What is an ETL pipeline? How does it differ from ELT?

**74.** What is a data pipeline? How do you ensure reliability and exactly-once processing?

**75.** What is CQRS (Command Query Responsibility Segregation)? When is it useful?

**76.** What is event sourcing? How does it differ from traditional CRUD?

**77.** What is a time-series database? When would you use it over a relational database?

**78.** What is a search engine (e.g., Elasticsearch, Solr)? How does inverted indexing work?

**79.** What is a materialized view? How does it improve read performance?

**80.** What is change data capture (CDC)? How does it sync data between systems?

## Questions 81-90: Scalability Patterns

**81.** What is consistent hashing? How does it minimize data redistribution?

**82.** What is a content-addressable storage? How does deduplication work?

**83.** What is a read replica? How do you handle replication lag?

**84.** What is a connection multiplexer? How does it reduce database connections?

**85.** What is request coalescing? How does it reduce duplicate work?

**86.** What is the fan-out pattern? Explain fan-out-on-write vs fan-out-on-read (e.g., Twitter timeline).

**87.** What is auto-scaling? What metrics should trigger scale-out vs scale-up?

**88.** What is geographic routing (geo-routing)? How do you serve users from the nearest region?

**89.** What is multi-tenancy? Explain shared database vs database-per-tenant.

**90.** What is the thundering herd problem? How do you prevent it?

## Questions 91-100: Basic System Design Scenarios

**91.** Design a URL shortener (like bit.ly). What are the key components?

**92.** Design a rate limiter. How would you implement it for a distributed system?

**93.** Design a notification system. How do you handle push, email, and SMS at scale?

**94.** Design a chat application. How do you handle real-time messaging and presence?

**95.** Design a file storage system (like Google Drive). How do you handle uploads and sharing?

**96.** Design a news feed (like Facebook/Twitter). How do you generate and rank the feed?

**97.** Design a search autocomplete system. How do you make suggestions fast?

**98.** Design a logging and monitoring system. How do you handle millions of log entries per second?

**99.** Design a task scheduler (like cron at scale). How do you ensure exactly-once execution?

**100.** Design a key-value store. What are the trade-offs between consistency and availability?

---

## Questions 101-120: Cryptography & Hashing

**101.** What is hashing? How does it differ from encryption? Give examples of each use case.

**102.** Explain MD5, SHA-1, SHA-256, and SHA-3. Which are still considered secure and why?

**103.** What is a hash collision? Why does it matter for security?

**104.** What is bcrypt? Why is it preferred over SHA-256 for password hashing?

**105.** What is a salt in password hashing? Why is it necessary?

**106.** What is a pepper? How does it differ from a salt?

**107.** What is a rainbow table attack? How do salted hashes prevent it?

**108.** What is symmetric encryption (AES)? How does it work and when do you use it?

**109.** What is asymmetric encryption (RSA, ECDSA)? Explain public/private key pairs.

**110.** What is the difference between encryption, encoding, and hashing?

**111.** What is AES-256? Why is it the industry standard for encryption at rest?

**112.** What is a digital signature? How does it prove authenticity and integrity?

**113.** What is a digital certificate? How does the certificate chain of trust work?

**114.** What is PKI (Public Key Infrastructure)? Explain CAs, root certificates, and intermediate certificates.

**115.** What is key management? How do you handle key rotation without downtime?

**116.** What is envelope encryption? How does AWS KMS use it?

**117.** What is HMAC (Hash-based Message Authentication Code)? When would you use it?

**118.** What is a nonce? How does it prevent replay attacks?

**119.** What is perfect forward secrecy? Why is it important for TLS?

**120.** What is end-to-end encryption? How does it work in messaging apps (e.g., Signal protocol)?

## Questions 121-140: Network Security

**121.** Explain the OSI model (7 layers). Which layers are most relevant for security?

**122.** What is the TCP/IP model? How does it differ from the OSI model?

**123.** What is a TCP 3-way handshake? How can it be exploited (SYN flood)?

**124.** What is TLS 1.3? What improved over TLS 1.2?

**125.** What is mTLS (mutual TLS)? When would you use it?

**126.** What is SSL termination? Where should you terminate SSL in your architecture?

**127.** What is a man-in-the-middle (MITM) attack? How does TLS prevent it?

**128.** What is DNS poisoning / DNS spoofing? How do you prevent it (DNSSEC)?

**129.** What is a DDoS attack? Explain L3/L4 vs L7 DDoS attacks and their mitigations.

**130.** What is a SYN flood attack? How do SYN cookies help?

**131.** What are IP tables / security groups / NACLs? How do they filter traffic at different levels?

**132.** What is a bastion host (jump box)? Why do you use one instead of exposing servers directly?

**133.** What is port forwarding and SSH tunneling? When would you use them?

**134.** What is NAT (Network Address Translation)? How does it relate to private/public IPs?

**135.** What is a subnet? How do you design subnets for public-facing vs private services?

**136.** What is CIDR notation? How do you calculate subnet ranges (e.g., /16, /24, /32)?

**137.** What is a VPN? How does site-to-site VPN differ from client VPN?

**138.** What is the difference between Layer 4 and Layer 7 load balancing from a security perspective?

**139.** What is HTTP Strict Transport Security (HSTS)? Why should you enable it?

**140.** What is certificate pinning? When should you use it and what are the risks?

## Questions 141-160: Application Security (OWASP & Beyond)

**141.** What is the OWASP Top 10? Name at least 5 and explain each briefly.

**142.** What is SQL injection? How do prepared statements / parameterized queries prevent it?

**143.** What is XSS (Cross-Site Scripting)? Explain stored, reflected, and DOM-based XSS.

**144.** What is CSRF (Cross-Site Request Forgery)? How do CSRF tokens work?

**145.** What is SSRF (Server-Side Request Forgery)? How did the Capital One breach exploit it?

**146.** What is broken authentication? What are the common mistakes in auth implementation?

**147.** What is insecure deserialization? How can it lead to remote code execution?

**148.** What is security misconfiguration? Give examples in web servers, databases, and cloud.

**149.** What is sensitive data exposure? How do you prevent it in API responses and logs?

**150.** What is broken access control? Explain IDOR (Insecure Direct Object Reference).

**151.** What is Content Security Policy (CSP)? How does it prevent XSS?

**152.** What is input validation vs input sanitization? Where should each be applied?

**153.** What is the principle of least privilege? How do you apply it to services and users?

**154.** What is defense in depth? Give an example with multiple security layers.

**155.** What is OAuth 2.0 vs OpenID Connect (OIDC)? When do you need which?

**156.** What is SAML? How does it differ from OAuth2/OIDC?

**157.** What is API key vs OAuth token vs JWT? When would you use each?

**158.** What is a WAF (Web Application Firewall)? What rules would you configure?

**159.** What is SAST vs DAST vs SCA? How do they fit into a CI/CD pipeline?

**160.** What is secret scanning? How do you prevent secrets from being committed to git?

## Questions 161-180: Kubernetes Basics & Networking

**161.** What is Kubernetes? Explain its architecture (control plane, nodes, kubelet, etcd).

**162.** What is a Pod in Kubernetes? Why is it the smallest deployable unit (not a container)?

**163.** What is a Deployment vs StatefulSet vs DaemonSet? When do you use each?

**164.** What is a Kubernetes Service? Explain ClusterIP, NodePort, and LoadBalancer types.

**165.** What is an Ingress in Kubernetes? How does it route external traffic to services?

**166.** What is a Namespace in Kubernetes? How do you use it for multi-tenancy?

**167.** What is a ConfigMap vs Secret in Kubernetes? How do you inject configuration into pods?

**168.** What is a PersistentVolume (PV) and PersistentVolumeClaim (PVC)? How does storage work in K8s?

**169.** What are liveness, readiness, and startup probes? What happens when each fails?

**170.** What is Helm? How does it simplify Kubernetes deployments?

**171.** What is kubectl? Name 10 essential commands you use daily.

**172.** What is a ReplicaSet? How does Kubernetes maintain the desired number of pods?

**173.** What is Horizontal Pod Autoscaler (HPA)? What metrics does it use to scale?

**174.** What is a Kubernetes Job vs CronJob? When would you use each?

**175.** What is container resource management? Explain CPU/memory requests vs limits.

**176.** What is a Kubernetes node drain? When and how do you drain a node safely?

**177.** What is a rolling update in Kubernetes? How do you configure maxSurge and maxUnavailable?

**178.** What is a Kubernetes rollback? How do you roll back a failed deployment?

**179.** What is etcd? Why is it critical for Kubernetes and how do you back it up?

**180.** What are Kubernetes labels and selectors? How do they enable service discovery?

## Questions 181-200: Kubernetes Security

**181.** What is RBAC (Role-Based Access Control) in Kubernetes? Explain Role, ClusterRole, RoleBinding.

**182.** What is a ServiceAccount in Kubernetes? Why should pods not use the default service account?

**183.** What is a NetworkPolicy in Kubernetes? How do you restrict pod-to-pod communication?

**184.** What is Pod Security Admission (PSA)? What replaced PodSecurityPolicy?

**185.** What is a Security Context in Kubernetes? How do you run containers as non-root?

**186.** What are Kubernetes Secrets? Why are they not truly secret by default and how do you fix that?

**187.** What is Sealed Secrets or External Secrets Operator? How do they solve K8s secret management?

**188.** What is image scanning in Kubernetes? How do you enforce only trusted images (admission controllers)?

**189.** What is OPA/Gatekeeper? How does it enforce policies in a Kubernetes cluster?

**190.** What is a service mesh (Istio, Linkerd)? How does it handle mTLS between pods?

**191.** What is the principle of least privilege for Kubernetes RBAC? Give a real example.

**192.** What is container escape? How do you prevent a container from accessing the host?

**193.** What is seccomp and AppArmor in Kubernetes? How do they restrict container syscalls?

**194.** What is supply chain security for containers? Explain image signing (Cosign/Sigstore).

**195.** What is Kubernetes audit logging? What events should you monitor?

**196.** What is a sidecar proxy for security? How does Envoy handle mTLS in Istio?

**197.** What is DNS in Kubernetes? How does CoreDNS resolve service names?

**198.** What is a Kubernetes Ingress Controller? How do you secure it with TLS and rate limiting?

**199.** What is etcd encryption? How do you encrypt secrets at rest in the Kubernetes data store?

**200.** How do you handle certificate management in Kubernetes? Explain cert-manager and Let's Encrypt integration.

## Questions 201-220: Cloud Security (AWS Focus)

**201.** What is IAM (Identity and Access Management)? Explain users, roles, and policies in AWS.

**202.** What is the difference between IAM role and IAM user? When do you use each?

**203.** What is STS (Security Token Service)? How does AssumeRole work?

**204.** What is a resource-based policy vs identity-based policy in AWS?

**205.** What is AWS Organizations and SCPs (Service Control Policies)? How do they enforce guardrails?

**206.** What is AWS CloudTrail? How does it help with security auditing?

**207.** What is AWS GuardDuty? How does it detect threats?

**208.** What is AWS WAF? How do you write rules to block common attacks?

**209.** What is AWS Shield? Explain Standard vs Advanced for DDoS protection.

**210.** What is AWS KMS (Key Management Service)? Explain CMKs, data keys, and key policies.

**211.** What is AWS Secrets Manager vs Parameter Store? When do you use each?

**212.** What is VPC security? Explain security groups, NACLs, VPC Flow Logs, and VPC endpoints.

**213.** What is AWS PrivateLink? How does it avoid exposing traffic to the public internet?

**214.** What is S3 bucket security? Explain bucket policies, ACLs, encryption, and Block Public Access.

**215.** What is RDS encryption? How do you encrypt data at rest and in transit for databases?

**216.** What is AWS Config? How does it detect non-compliant resources?

**217.** What is the shared responsibility model in AWS? What is your responsibility vs AWS's?

**218.** What is cross-account access in AWS? How do you securely share resources between accounts?

**219.** What is a VPC peering vs Transit Gateway? When would you use each?

**220.** What is AWS Inspector? How does it scan for vulnerabilities in EC2 and container images?

## Questions 221-250: DevSecOps & Network Fundamentals

**221.** What is DevSecOps? How does it differ from traditional security approaches?

**222.** What is shift-left security? How do you integrate security into the development lifecycle?

**223.** What is a CI/CD security pipeline? What security gates should each stage have?

**224.** What is SAST (Static Application Security Testing)? Name tools (SonarQube, Semgrep, Checkmarx).

**225.** What is DAST (Dynamic Application Security Testing)? How does it differ from SAST?

**226.** What is SCA (Software Composition Analysis)? How does it detect vulnerable dependencies?

**227.** What is container image scanning? Compare Trivy, Snyk, and Grype.

**228.** What is Infrastructure as Code (IaC) scanning? How do tools like Checkov and tfsec work?

**229.** What is git secret scanning? How do tools like GitLeaks and TruffleHog detect leaked credentials?

**230.** What is dependency confusion / typosquatting attack? How do you prevent it?

**231.** What is a software bill of materials (SBOM)? Why is it important for supply chain security?

**232.** What is SLSA (Supply-chain Levels for Software Artifacts)? Explain the 4 levels.

**233.** What is Docker content trust? How does image signing work?

**234.** What is the CIS Benchmark? How do you use it to harden Docker, Kubernetes, and Linux?

**235.** What is compliance as code? How do you automate SOC2/PCI-DSS/HIPAA checks?

**236.** What is vulnerability management? How do you prioritize CVEs (CVSS scoring)?

**237.** What is a security incident response plan? What are the 6 phases (NIST)?

**238.** What is penetration testing vs vulnerability scanning? When do you do each?

**239.** What is threat modeling? Explain STRIDE methodology.

**240.** What is a security champion program? How do you embed security in engineering teams?

**241.** What is an IP address? Explain IPv4 vs IPv6 and private vs public IP ranges.

**242.** What is a MAC address? How does ARP (Address Resolution Protocol) work?

**243.** What are well-known ports? Name 10 important ones (22, 80, 443, 3306, 5432, 6379, etc.).

**244.** What is HTTP vs HTTPS? What is the default port for each?

**245.** What is HTTP/2 vs HTTP/3? What are the key improvements in each?

**246.** What are HTTP methods (GET, POST, PUT, PATCH, DELETE)? Explain idempotency for each.

**247.** What are HTTP status codes? Explain 2xx, 3xx, 4xx, 5xx with examples.

**248.** What is a cookie vs session vs local storage? Security implications of each?

**249.** What is the difference between stateful and stateless protocols?

**250.** What is a CORS preflight request? What triggers it and how does the browser handle it?

## Questions 251-280: Authentication Deep Dive & Advanced Networking

**251.** What is DNS round-robin? How is it used for basic load balancing?

**252.** What is traceroute/tracert? How do you diagnose network latency issues?

**253.** What is a proxy server? Explain forward proxy, reverse proxy, and transparent proxy.

**254.** What is BGP (Border Gateway Protocol)? Why is it called the "internet's routing protocol"?

**255.** What is DHCP? How does a device get an IP address automatically?

**256.** What is a socket? How does socket programming work at a high level?

**257.** What is the difference between L2 switch, L3 router, and L7 load balancer?

**258.** What is network latency? Explain RTT (Round Trip Time) and how to measure it.

**259.** What is bandwidth vs throughput? Why can actual throughput be lower than bandwidth?

**260.** What is a CDN edge server? How does anycast routing direct users to the nearest one?

**261.** What is multi-factor authentication (MFA)? Explain TOTP, SMS, WebAuthn/FIDO2.

**262.** What is SSO (Single Sign-On)? How does it work with SAML and OIDC?

**263.** What is a session fixation attack? How do you prevent it?

**264.** What is a brute force attack? How do account lockout and rate limiting help?

**265.** What is credential stuffing? How does it differ from brute force?

**266.** What is passwordless authentication? Explain magic links, passkeys, and biometrics.

**267.** What is token-based authentication? Explain access token vs refresh token lifecycle.

**268.** What is token revocation? How do you invalidate JWTs (blacklist, short expiry, reference tokens)?

**269.** What is OAuth 2.0 client credentials flow? When do you use it (service-to-service auth)?

**270.** What is OAuth 2.0 PKCE? Why is it required for SPAs and mobile apps?

**271.** What is API authentication? Compare API keys, OAuth tokens, and mutual TLS.

**272.** What is RBAC vs ABAC vs ReBAC? When would you use each access control model?

**273.** What is an identity provider (IdP)? Compare Keycloak, Auth0, AWS Cognito, and Okta.

**274.** What is JWT structure? Explain header, payload, and signature. What should never be in a JWT?

**275.** What is a JWT algorithm (HS256 vs RS256 vs ES256)? Which is recommended and why?

**276.** What is the "none" algorithm attack in JWT? How do you prevent it?

**277.** What is scope-based authorization in OAuth2? How do you design scopes for an API?

**278.** What is impersonation / delegation in authentication? When is it legitimate?

**279.** What is a session store? Compare in-memory, Redis, and database-backed sessions.

**280.** What is Cross-Origin authentication? How do you handle auth in a microservices architecture?

## Questions 281-300: Security Scenarios & Practical Situations

**281.** Your application is being targeted by a credential stuffing attack. What immediate steps do you take?

**282.** You discover an API endpoint exposing user emails without authentication. How do you fix it and prevent recurrence?

**283.** A developer accidentally pushed an AWS access key to a public GitHub repo. What do you do?

**284.** Your Redis instance is accessible from the public internet. How do you secure it?

**285.** A penetration test reveals your application is vulnerable to SQL injection. Walk through the remediation.

**286.** Your TLS certificate expired in production and users see browser warnings. How do you prevent this?

**287.** You need to rotate database credentials without downtime. How do you do it?

**288.** A third-party library you depend on has a critical CVE. What is your process?

**289.** Your Kubernetes cluster has pods running as root. How do you fix this at scale?

**290.** You need to implement end-to-end encryption for a chat feature. What is your approach?

**291.** Your load balancer logs show a massive spike from a single IP. What do you do?

**292.** An internal service is making requests to an external IP it shouldn't. How do you investigate?

**293.** You need to pass a SOC2 audit. What are the key controls you need to demonstrate?

**294.** A new microservice needs access to another team's database. What is the secure approach?

**295.** Your Docker images have critical vulnerabilities from the base image. How do you handle it?

**296.** You need to implement logging that captures security events but not PII. How do you design it?

**297.** Your API is being scraped by bots. How do you detect and prevent it without affecting real users?

**298.** A developer asks to disable HTTPS for local testing. What is the safe way to handle this?

**299.** You need to encrypt sensitive columns in an existing database with millions of rows. What is your migration strategy?

**300.** Design a security architecture for a new microservices application: authentication, authorization, encryption, secrets management, network security, and monitoring.

---

## Questions 301-350: Architecture Trade-offs & "When Should You..." Decisions

### Architecture Style Trade-offs

**301.** Monolith vs Microservices: What are the signals that tell you it's time to break a monolith? What are the signals that microservices are overkill?

**302.** When should a startup use a monolith vs microservices? At what team size / user scale does the switch make sense?

**303.** What is a modular monolith? When is it a better choice than jumping straight to microservices?

**304.** Microservices vs SOA (Service-Oriented Architecture): What's the real difference and when does it matter?

**305.** Serverless vs containers: When should you use Lambda/Cloud Functions vs ECS/Kubernetes? What are the cost, latency, and operational trade-offs?

**306.** When should you use a monorepo vs polyrepo for microservices? What are the CI/CD implications of each?

**307.** When should you use a BFF (Backend-for-Frontend) pattern? When does it add unnecessary complexity?

**308.** When should you use an API gateway vs direct service-to-service calls? What's the performance cost?

**309.** When should you use sidecar pattern vs library-based approach for cross-cutting concerns (logging, auth, tracing)?

**310.** When should you merge two microservices back into one? What are the signs of over-decomposition?

### Communication Trade-offs

**311.** REST vs gRPC: When should you use each? What are the trade-offs in performance, tooling, and developer experience?

**312.** REST vs GraphQL: When should you use each? When does GraphQL add more problems than it solves?

**313.** Synchronous (REST/gRPC) vs Asynchronous (message queue): How do you decide which communication pattern to use between services?

**314.** Event-driven vs request-driven architecture: When should you switch from REST calls to events? What are the debugging challenges?

**315.** Kafka vs SQS vs RabbitMQ: When should you use each? What are the ordering, throughput, and operational trade-offs?

**316.** Pub/Sub vs Point-to-Point messaging: When do you need fan-out to multiple consumers vs exactly one consumer?

**317.** Webhooks vs polling vs WebSockets: When should you use each for real-time updates?

**318.** Choreography vs orchestration in microservices: When should each service react to events independently vs having a central coordinator?

**319.** When should you use batch processing vs stream processing? What if you need both (Lambda architecture)?

**320.** When should you use a shared event bus (Kafka) vs direct service-to-service events? What are the coupling trade-offs?

### Database Trade-offs

**321.** SQL vs NoSQL: What specific questions should you ask about your data to make this decision?

**322.** PostgreSQL vs MySQL: When would you choose one over the other? What are the feature differences that matter?

**323.** DynamoDB vs MongoDB vs Cassandra: When should you use each NoSQL option?

**324.** When should you use a database-per-service vs shared database in microservices? What are the data consistency trade-offs?

**325.** When should you denormalize your database? What is the cost of denormalization vs the cost of JOINs?

**326.** When should you shard your database vs using read replicas? What scale triggers each?

**327.** When should you use Redis as a primary data store vs just a cache?

**328.** When should you use a search engine (Elasticsearch/Solr) vs database full-text search?

**329.** When should you use a time-series database (InfluxDB, TimescaleDB) vs storing metrics in PostgreSQL?

**330.** ACID vs BASE: When is eventual consistency acceptable? When must you have strong consistency?

### Caching Trade-offs

**331.** When should you add caching vs optimizing the underlying query? How do you decide?

**332.** Local cache (in-memory) vs distributed cache (Redis): When do you use each? What about consistency between instances?

**333.** Cache-aside vs read-through vs write-through: How do your read/write patterns determine the right strategy?

**334.** Short TTL vs long TTL: How do you balance freshness vs performance? What's the cost of stale data?

**335.** CDN caching vs application caching vs database caching: At which layer should you cache for your use case?

### Deployment & Infrastructure Trade-offs

**336.** ECS vs Kubernetes: When is ECS "good enough" vs when do you need full Kubernetes?

**337.** Managed services (RDS, ElastiCache) vs self-hosted: When is the premium worth it? When should you self-host?

**338.** Multi-region vs single-region: When is multi-region deployment justified? What is the real cost (money, complexity, consistency)?

**339.** Blue-green vs canary vs rolling deployment: How do your traffic patterns and risk tolerance determine the right strategy?

**340.** Infrastructure as Code (Terraform) vs ClickOps (AWS Console): When is IaC overhead not worth it?

### Scalability Trade-offs

**341.** Vertical scaling vs horizontal scaling: When should you scale up before scaling out? What are the limits of each?

**342.** Synchronous writes vs async writes (eventual consistency): When can you afford to lose a few writes vs when must every write be confirmed?

**343.** Push model vs pull model: When should the server push data to clients vs clients pull on demand?

**344.** Precomputation vs on-the-fly computation: When should you precompute and store results (materialized views) vs compute at request time?

**345.** Fan-out-on-write vs fan-out-on-read: How do you choose for a social media feed? What about a mix (hybrid)?

### Design Pattern Trade-offs

**346.** CQRS: When does separating read and write models pay off? When is it over-engineering?

**347.** Event sourcing vs traditional CRUD: When should you store events vs just the current state? What's the operational complexity?

**348.** Saga pattern vs two-phase commit: When should you use each for distributed transactions? What are the failure modes?

**349.** Circuit breaker vs retry with backoff: When do you cut off a failing dependency vs keep trying? Can you use both?

**350.** Feature flags vs branching: When should you use feature flags to control releases? When do they become tech debt?

## Questions 351-360: "How Did You Secure..." (Experience-Based)

**351.** How did you secure your APIs? Walk me through every layer from the client to the database.

**352.** How did you prevent unauthorized access to internal microservices that should not be publicly accessible?

**353.** How did you handle API authentication between your frontend and backend? What about service-to-service auth?

**354.** How did you protect your application against the OWASP Top 10 vulnerabilities? Which ones were most relevant to your project?

**355.** How did you secure your database? (network access, encryption at rest, encryption in transit, credential rotation, audit logging)

**356.** How did you secure your CI/CD pipeline? What prevents a malicious actor from injecting code through your build process?

**357.** How did you secure your Docker containers in production? (non-root, image scanning, minimal base images, read-only filesystem)

**358.** How did you handle security logging and alerting? What security events did you monitor and what triggered alerts?

**359.** How did you secure sensitive data in your cloud environment? (S3 buckets, environment variables, database backups, log files)

**360.** How did you handle a security incident or vulnerability discovery in your project? Walk me through the process from detection to resolution.
