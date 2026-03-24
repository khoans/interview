**13. Docker Networking**
Explain Docker networking modes (bridge, host, overlay). How do containers communicate with each other?

========== THIS SECTION IS THE ANSWER ==========

Bridge is the default — containers get their own IP on an isolated network, talk to each other by container name via Docker's built-in DNS. Host removes network isolation, container uses the host's network directly — better performance but no port isolation. Overlay spans multiple Docker hosts, used in Docker Swarm or multi-node setups.

In our local development, we used bridge networking with Docker Compose — services referenced each other by name (e.g., `http://search-service:8080`). In production on ECS, AWS handled the networking with its own VPC and service discovery.

The trade-off: bridge is safe and isolated but adds a small network hop. Host mode eliminates that overhead but means port conflicts if two containers want the same port. For most applications, bridge with Docker Compose DNS is the right choice for development; in production, your orchestrator (ECS, K8s) manages networking for you.
