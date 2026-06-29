# Healthcare App GitOps Pipeline

A production-ready DevSecOps pipeline orchestrating a robust Java Spring Boot backend healthcare application. This project focuses on automated continuous integration, strict vulnerability shifting-left gates, compliance reporting, and automated GitOps manifest synchronization.

## 🛠️ Core Technology Stack

### 🚀 Application Layer
* **Backend Framework:** Java Spring Boot (RESTful Microservices architecture)
* **Build Automation:** Maven (Lifecycle tracking & dependency mapping)
* **Testing Engine:** JUnit (Automated code unit testing)

### ☁️ Cloud & DevSecOps Infrastructure
* **Version Control:** Git & GitHub
* **CI Automation Server:** Jenkins (Declarative Pipeline-as-Code)
* **Static Code Analysis:** SonarQube & Automated Quality Gates
* **Containerization:** Docker (Multi-stage build runtime footprint compression)
* **Security Scanning:** Aqua Security Trivy (Image filesystem vulnerability auditing)
* **Cloud Infrastructure:** Amazon Web Services (AWS S3 for secure compliance report archival)
* **Container Registry:** Docker Hub
* **Orchestration Target:** Kubernetes (Manifest Architecture Configuration)

---

## 🏗️ Operational Pipeline-as-Code Architecture

The platform architecture utilizes an event-driven automation lifecycle to ensure verified code deployments:

1. **Webhook Code Triggers:** Source modifications pushed to the repository automatically alert the Jenkins automation server via webhooks to launch an isolated pipeline execution run.
2. **Compilation & Quality Controls:** Maven compiles the Spring Boot backend while JUnit triggers test-suite verification. Any assertion anomaly drops the execution thread instantly.
3. **Static Security Profiling:** The codebase passes through a SonarQube Scanner. Code execution is evaluated against strict Quality Gates measuring bugs, vulnerabilities, and code smell concentrations.
4. **Optimized Multi-Stage Container Build:** Docker isolates compilation dependencies from final runtimes. It maps the jar execution layers on top of a lightweight Alpine runtime layer exposing port `8082`.
5. **Vulnerability Auditing:** Trivy reviews the final image layer configurations, exporting a structured HTML security log file. This compliance audit report is mirrored straight into an **AWS S3 Bucket** for historic review.
6. **Registry Registry Tracking:** Verified images are tagged using tracking variables (`${BUILD_ID}-${GIT_COMMIT}`) and pushed directly into the personal Docker Hub registry (`sajankumar786/medicure`).
7. **GitOps Manifest Update:** Jenkins executes localized parsing scripts (`sed`) to update your exact image tags directly inside the `/k8s/test-deployment.yaml` and `/k8s/prod-deployment.yaml` cluster configuration files on the main branch.

---

## 📁 Project Directory Structure

```text
├── src/                      # Java Spring Boot application source code (Controllers, Services)
├── pom.xml                   # Maven dependencies and lifecycle metadata
├── Dockerfile                # Multi-stage optimized Java runtime build configuration
├── Jenkinsfile               # Declarative automation script tracking code security gates
└── k8s/                      # Target environment deployment orchestration folder
    ├── test-deployment.yaml  # Test namespace manifest layout (Port: 30038)
    └── prod-deployment.yaml  # Production namespace manifest layout (Port: 30039)
