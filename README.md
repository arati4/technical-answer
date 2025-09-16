QUE) Can you describe a challenging incident in production you managed in AWS/Kubernetes, what tools you used for troubleshooting, and how you reduced MTTR?
ANS= In production, we faced a critical issue where one of our Java-based microservices in Kubernetes went into continuous Full GC and stopped serving requests. This caused pod restarts and impacted latency for end-users.

For troubleshooting, I used:
	•	Dynatrace for JVM metrics and garbage collection analysis
	•	kubectl to check pod status and logs
	•	CloudWatch for node-level monitoring
	•	Grafana with Prometheus metrics to correlate memory usage with traffic

The root cause was a memory leak due to improper caching. To reduce MTTR, we:
	1.	Added HPA (Horizontal Pod Autoscaler) to auto-scale pods during load spikes.
	2.	Implemented liveness and readiness probes so that failing pods were quickly replaced.
	3.	Tuned JVM heap settings via JAVA_OPTS.

This reduced MTTR significantly since alerts were routed to PagerDuty, and auto-remediation (restart on probe failure) avoided manual intervention.




QUE) Explain your experience in building/optimizing CI/CD pipelines (tools used, rollback strategy, and security/observability integrations)
ANS= I built and optimized CI/CD pipelines using Jenkins and later migrated to GitHub Actions for better integration with GitHub repos.
	•	Tools used: Jenkins, GitHub Actions, ArgoCD for GitOps, SonarQube for code quality, Trivy for container image scanning, and Helm for Kubernetes deployments.
	•	Rollback strategy: We used Helm chart versioning and ArgoCD rollback to previous stable manifests. Also, blue-green deployments were enabled for critical services, so traffic could be instantly switched back if issues were found.
	•	Security/Observability:
	•	Integrated Snyk and Trivy in the pipeline for vulnerability scanning.
	•	Configured Prometheus + Grafana + Loki for observability, and alerts were sent via Slack/PagerDuty.
	•	Secrets were managed in AWS Secrets Manager instead of being stored in repos.

This automation reduced deployment failures by ~30% and decreased lead time for changes from hours to ~15 minutes.



QUE) Share an example where you used Infrastructure as Code (Terraform/Ansible) to automate cloud infrastructure at scale. What problems did it solve?
ANS= In one project, we had multiple teams manually provisioning AWS resources (EC2, RDS, EKS, S3) which caused drift, inconsistencies, and higher costs.

I implemented Terraform modules to standardize provisioning:
	•	Reusable Terraform modules for VPC, EKS, and RDS.
	•	Workspaces for environment separation (dev, QA, prod).
	•	Remote backend in S3 with DynamoDB for state locking.

For configuration management, we used Ansible to:
	•	Install dependencies on EC2 nodes
	•	Push application configs
	•	Rotate secrets

Problems solved:
	•	Eliminated manual provisioning and reduced human error.
	•	Achieved 100% infra reproducibility across environments.
	•	Cut down infra setup time from 2–3 days to under 1 hour.
	•	Enabled cost visibility and tagging policies across AWS.
