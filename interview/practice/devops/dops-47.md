**47. GitOps**
What is GitOps? How does it differ from traditional CI/CD? What are its advantages for Kubernetes deployments?

========== THIS SECTION IS THE ANSWER ==========

GitOps uses Git as the single source of truth for infrastructure and application state. Instead of a pipeline pushing changes to servers, an operator (like ArgoCD or Flux) watches the Git repo and pulls changes — reconciling the actual state with the desired state in Git. Traditional CI/CD pushes; GitOps pulls.

In our project, we didn't use full GitOps (we used ECS, not Kubernetes), but we applied the principle: all infrastructure was in Git (CloudFormation/Terraform), all config changes went through PRs, and nothing was applied manually. If someone changed something in the AWS console, the next Terraform apply would revert it.

The trade-off: GitOps is excellent for Kubernetes — ArgoCD watches your manifests repo and auto-syncs the cluster, giving you audit trail, easy rollback (git revert), and drift detection for free. But it adds another tool to manage, and debugging sync failures can be confusing. For non-K8s environments, applying GitOps principles (everything in Git, no manual changes) is more practical than adopting a full GitOps tool.
