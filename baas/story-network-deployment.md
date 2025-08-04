# User Story: Automated Private Network Deployment

**As a:** Client (Developer)

**I want to:** Be able to request the creation of a new private blockchain network through a single API call.

**So that:** I can quickly get a ready-to-use, isolated blockchain environment without manual setup.

## Acceptance Criteria
- A `POST` request to the `/create-network` endpoint with specified parameters (e.g., number of validators) initiates the provisioning process.
- The system successfully deploys a new VPC on GCP for the client.
- The system deploys the specified number of Hyperledger Besu validator nodes in Docker containers on GCE VMs.
- The system deploys at least one RPC node.
- The system deploys a BlockScout instance connected to the RPC node.
- All validator keys are created and stored securely in GCP KMS.
- The API returns the RPC endpoint and Block Explorer URL upon successful creation.
- The process is fully automated via Terraform.
