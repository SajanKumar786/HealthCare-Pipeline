# HealthCare-App: End-to-End DevSecOps GitOps Pipeline

An enterprise-grade, cloud-native automation and infrastructure project orchestrating a full-stack Java Spring Boot healthcare booking system. This platform is built leveraging DevSecOps practices and GitOps continuous delivery models to enforce strict zero-downtime rolling updates and security gates.

## 🛠️ Technology Stack & Tools
* **Version Control:** Git, GitHub
* **CI Engine:** Jenkins (Declarative Pipelines)
* **Build & Test:** Maven, JUnit
* **Static Code Analysis:** SonarQube & Quality Gates
* **Containerization:** Docker (Multi-stage architecture optimization)
* **Vulnerability Scanning:** Aqua Security Trivy
* **Cloud Storage:** Amazon S3 (Artifact & security report compliance archiving)
* **Container Registry:** Docker Hub
* **Orchestration Cluster:** Kubernetes (EKS Engine managed via kubectl)
* **Continuous Deployment (GitOps):** ArgoCD
* **Cluster Monitoring:** Prometheus & Grafana
* **Team Collaboration Alerts:** Slack App Integration

---

## 🏗️ Operational Architecture & Pipeline Flow

The entire application lifecycle is automated into an event-driven framework following these execution stages:

1. **Inbound Webhook Trigger:** Code updates pushed to the GitHub application repository alert the local Jenkins automation engine via configured webhooks to initiate an isolated build sequence.
2. **Compilation & Unit Testing:** Maven compiles the source code. If compilations or internal JUnit assertion tests fail, the build halts instantly and broadcasts alerts via Slack.
3. **Static Security Analysis:** The system transfers analytics variables into a SonarQube Scanner. The build checks code rules against custom quality gates tracking bugs, vulnerabilities, and code smell concentrations.
4. **Multi-Stage Container Build:** Docker isolates compilation tools from final production images. It maps the runtime on top of a lightweight Alpine layer exposing container network socket port `8082`.
5. **Vulnerability Scanning & Asset Archival:** Trivy reviews built images for security flaws, printing out structured HTML auditing logs. These compliance files are immediately copied into an AWS S3 bucket for auditing history records.
6. **Registry Delivery:** Verified images are tagged using tracking variables (`${BUILD_ID}-${GIT_COMMIT}`) and pushed directly into the personal Docker Hub registry (`sajankumar786/medicure`).
7. **Infrastructure GitOps Update:** The pipeline uses native `sed` scripts to overwrite image reference coordinates inside the localized `/k8s/test-deployment.yaml` and `prod-deployment.yaml` manifest configurations.
8. **Automated Manifest Synchronization:** Updated configurations commit back into the main codebase branch. ArgoCD evaluates cluster tracking drift, executing automated zero-downtime rolling upgrades to keep live running environment state completely synced.

---

## 📁 Repository Directory Structure

```text
├── src/                      # Java Spring Boot application backend source code
├── pom.xml                   # Maven project dependencies and version configurations
├── Dockerfile                # Multi-stage optimized Java runtime build automation layers
├── Jenkinsfile               # Declarative pipeline script handling security gates & deployment
└── k8s/                      # Target environment orchestration folder
    ├── test-deployment.yaml  # Deployment manifest for test namespace (Port: 30038)
    └── prod-deployment.yaml  # Deployment manifest for prod namespace (Port: 30039)