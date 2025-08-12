# Epics and Tasks for BaaS Platform Development

This document breaks down the development work for the BaaS platform into epics and detailed tasks for the developer, based on the architecture document.
---

## Epic 1: BaaS Control Plane Implementation

**User Story:** As a platform operator, I want a robust control plane to automate the creation, management, and monitoring of client-specific private blockchain networks.

### Tasks:

1.  **Develop the Management API (Container: `api`) with Nest.js**
    *   [ ] **Task 1.1:** Set up the project using the **Nest.js** framework (`@nestjs/cli`).
    *   [ ] **Task 1.2:** Configure **Prisma** as the ORM. Define the schema for client network metadata (ID, status, endpoints, owner) in `schema.prisma` and generate the Prisma Client.
    *   [ ] **Task 1.3:** Connect the application to a **PostgreSQL** database using Prisma.
    *   [ ] **Task 1.4:** Design and implement the REST API endpoints (e.g., in a `NetworkController`). Start with `POST /create-network`.
    *   [ ] **Task 1.5:** Implement request validation using Nest.js's built-in `ValidationPipe` and `class-validator` decorators for DTOs (Data Transfer Objects).
    *   [ ] **Task 1.6:** Integrate **Swagger** (`@nestjs/swagger`) to automatically generate and serve API documentation.
    *   [ ] **Task 1.7:** Implement the logic to trigger the provisioning pipeline in the Provisioning Service (e.g., via a message queue or a direct API call).

2.  **Implement the Provisioning Service (Container: `provisioner`)**
    *   [ ] **Task 2.1:** Set up a new project for the Provisioning Service using **Node.js**.
    *   [ ] **Task 2.2:** Develop a wrapper around Terraform to execute IaC scripts programmatically.
    *   [ ] **Task 2.3:** Create a job processing system to handle incoming requests from the Management API.
    *   [ ] **Task 2.4:** Implement status tracking for provisioning jobs (e.g., `pending`, `in_progress`, `completed`, `failed`).
    *   [ ] **Task 2.5:** Implement logic to report back the results of a deployment (e.g., RPC endpoints, explorer URL) to the Management API.

---

## Epic 2: Client Infrastructure Automation with Terraform

**User Story:** As a platform operator, I want to use Terraform to fully automate the deployment of an isolated, secure, and functional private EVM network for each client.

### Tasks:

1.  **Develop Core Terraform Modules**
    *   [ ] **Task 1.1:** Create a Terraform module to set up the client's network infrastructure:
        *   [ ] Define a VPC.
        *   [ ] Define separate subnets for validators and tooling (`subnet_validators`, `subnet_tools`).
        *   [ ] Configure firewall rules to enforce the security policies described in the architecture.
    *   [ ] **Task 1.2:** Create a Terraform module for GCP Cloud KMS to:
        *   [ ] Create a Key Ring for each client network.
        *   [ ] Generate the required number of cryptographic keys for the validators.
    *   [ ] **Task 1.3:** Create a Terraform module for GCE Virtual Machines that:
        *   [ ] Can be configured to use a specific machine type (e.g., `n1-standard-2`).
        *   [ ] Is provisioned with a startup script to install Docker.
        *   [ ] Is assigned a dedicated service account with the principle of least privilege.

2.  **Automate Hyperledger Besu Deployment**
    *   [ ] **Task 2.1:** Create a `genesis.json` file template that can be dynamically populated with the validator addresses from the KMS module.
    *   [ ] **Task 2.2:** Develop a startup script for validator nodes that:
        *   [ ] Pulls the `hyperledger/besu` Docker image.
        *   [ ] Starts the Besu container configured as a validator, using the generated `genesis.json`.
        *   [ ] Configures Besu to use the corresponding key from Cloud KMS for signing blocks.
    *   [ ] **Task 2.3:** Develop a startup script for RPC nodes that starts Besu in RPC mode.

3.  **Automate Tooling Deployment**
    *   [ ] **Task 3.1:** Develop a startup script for the tooling VM that deploys **BlockScout**.
        *   [ ] The script should pull the `blockscout/blockscout` Docker image.
        *   [ ] It should configure BlockScout to connect to the RPC node.
    *   [ ] **Task 3.2:** Develop a startup script to deploy a simple **Faucet** service (if required).

---

## Epic 3: Security and Monitoring Implementation

**User Story:** As a platform operator and a client, I want the system to be secure by default and provide comprehensive monitoring to ensure network health and performance.

### Tasks:

1.  **Implement Security Measures**
    *   [ ] **Task 1.1:** Configure IAM roles and service accounts with the least privilege required for each component (Management API, Provisioner, Besu Nodes).
    *   [ ] **Task 1.2:** In the GCE Terraform module, ensure that the service account attached to Besu validator VMs has the `cloudkms.signer` role and nothing more.
    *   [ ] **Task 1.3:** Implement rate limiting on the Management API and the RPC nodes to prevent abuse.
    *   [ ] **Task 1.4:** Set up a process for periodic backups of blockchain data from GCE persistent disks to Google Cloud Storage.

2.  **Set Up Monitoring and Logging**
    *   [ ] **Task 2.1:** Create a Terraform module or script to deploy **Prometheus** and **Grafana** as Docker containers on a dedicated tooling VM.
    *   [ ] **Task 2.2:** Configure Prometheus to automatically discover and scrape the `/metrics` endpoint from all Besu nodes in the client's network.
    *   [ ] **Task 2.3:** Create a default Grafana dashboard with key metrics as described in the architecture (validator status, block height, TPS, etc.).
    *   [ ] **Task 2.4:** Configure the Google Cloud Logging agent on all GCE VMs to collect logs from Docker containers.
    *   [ ] **Task 2.5:** Create log-based metrics and alerts in Google Cloud Operations for critical errors (e.g., a validator node crashing).
