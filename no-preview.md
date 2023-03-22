# Crossplane is great, but maybe it is a bit risky to use for critical infrastructure

## Quick recap: What is Crossplane?
Crossplane is a control plane that runs inside Kubernetes, which ensures that the external resources running in the cloud provider are in sync with the state declared in Kubernetes. Crossplane manages the entire lifecycle of the resources declared. All resources managed by Crossplane are declared in manifests stored in Kubernetes. Crossplane was accepted to CNCF on June 23, 2020, and is at the [Incubating](https://www.cncf.io/projects/crossplane/) project maturity level. 

Just like Terraform, Crossplane has the concept of [providers](https://www.cncf.io/projects/crossplane/). Crossplane-providers work similarly to how Terraform-providers work. Service providers can create a plugin that integrates with Crossplane providing the user the ability to provision external resources on their infrastructure. It is now up to the service provider to manage and ensure that the state running on their infrastructure matches the desired state declared in the Kubernetes cluster. Using Crossplane for infrastructure management makes it possible to provision resources on multiple cloud providers at once, which can be beneficial because different cloud providers have different offerings. Currently, Crossplane supports AWS, GCP, and Microsoft Azure as cloud providers. A [DigitalOcean provider](https://www.digitalocean.com/blog/announcing-the-digitalocean-crossplane-provider) is also in active development.

## Compared to Terraform
As I see it, one of the biggest drawbacks of using Crossplane for infrastructure-management compared to Terraform is that there is no option to preview changes before they are applied.

With Terraform, the developer can run terraform plan to see a preview of the changes that will happen. This makes it possible to review the changes first before committing to the new configuration.

With Crossplane, there is no such feature. Crossplane cannot show a preview of the resources it is going to create/modify/delete. So, the developers can only apply the manifests and hope that they did it correctly. This makes Crossplane risky to use for critical infrastructure.
This issue is also described on [GitHub](https://github.com/crossplane/crossplane/issues/1805).

## Atlantis

Atlantis is a tool for automating Terraform workflows, which enables users to collaborate on infrastructure changes with appropriate approvals, feedback, and version-controlled history. It provides a standard workflow for Terraform, making it easier to manage multiple environments, review code changes, and apply changes in staging and production environments.

Atlantis will automatically run a `terraform plan` when a PR is raised from a feature branch. Now reviewers can see the changes that will happen to the infrastructure before the configuration is applied.

But no such tool exists for Crossplane because Crossplane does not have a plan step.

## Why not just test your crossplane changes on staging environment before you merge to production!?

Well, this is a valid argument. You could limit the risk of applying the wrong configuration by testing it on staging first. But even though the staging is supposed to be as close to the production environment as possible, it will never be identical. So there is always a risk of breaking the production environment with the new configuration.

## The future
I do not know if there will be a preview-feature or "dry-run"-feature (where it runs the new configuration but without changing anything) in Crossplane in the future. Also, I do not know how it would work, but I imagine preview-features are inherently difficult to make in control planes because it is not obvious when in the process, a preview of the changes should be reviewed by a developer.

I would assume that the preview-feature would be implemented in the Crossplane CLI, but I do not know if it is possible to implement it in the Crossplane CLI without making changes to the Crossplane core.

## Conclusion 
Crossplane has no preview-feature for reviewing changes applied to the infrastructure configuration. This makes Crossplane risky to use for critical infrastructure, and I would consider this one of the biggest drawbacks of using Crossplane for infrastructure-management.