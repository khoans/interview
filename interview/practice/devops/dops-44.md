**44. SSL/TLS Certificates**
How do SSL/TLS certificates work in a deployment pipeline? How do you automate certificate renewal? What is Let's Encrypt?

========== THIS SECTION IS THE ANSWER ==========

SSL/TLS encrypts traffic between client and server. The certificate proves your server's identity. Let's Encrypt is a free Certificate Authority that issues certificates with automated validation — no manual process needed. Certificates expire (typically 90 days for Let's Encrypt, 1 year for paid CAs).

In our project, we used AWS Certificate Manager (ACM) for all public-facing services. ACM issues free certificates and auto-renews them — zero manual work. Certificates were attached to our ALB, which terminated SSL so our backend services didn't handle encryption themselves. For internal service-to-service communication, we used private certificates from ACM Private CA.

The trade-off: SSL termination at the load balancer means traffic between ALB and your containers is unencrypted by default. For most internal VPCs that's acceptable, but for compliance-sensitive workloads (PCI, HIPAA), you need end-to-end encryption. We enabled HTTPS between ALB and containers only for our payment-related services — it added latency but met compliance requirements.
