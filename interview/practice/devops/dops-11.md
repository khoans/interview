**11. Docker vs Virtual Machines**
What is the fundamental difference between Docker containers and virtual machines? When would you choose one over the other?

========== THIS SECTION IS THE ANSWER ==========

Containers share the host OS kernel and isolate at the process level — lightweight, start in seconds, use less memory. VMs run a full guest OS on a hypervisor — heavier but provide stronger isolation since each VM has its own kernel.

In our project, all microservices ran in Docker containers on ECS. But for our Solr search engine cluster, we used EC2 instances (essentially VMs) because Solr needed specific kernel tuning (file descriptors, memory mapping) and persistent local storage that was easier to manage on VMs.

The trade-off: containers are great for stateless microservices — fast startup, easy scaling, reproducible environments. VMs are better when you need full OS control, different kernel versions, or strong security isolation between tenants. Most modern backend services fit containers, but legacy workloads or stateful systems sometimes need VMs.
