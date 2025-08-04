# GCP Resource Configurations

## Minimum Compressed Configuration

This configuration minimizes resource usage while maintaining functionality for a small-scale network (e.g., 3 validators) suitable for testing or lightweight production use. It leverages GCP's free tier where possible and assumes low traffic.

| Resource Type         | Configuration Details |
|-----------------------|-----------------------|
| **Compute Engine (GCE)** | 3 VMs: 2x e2-standard-2 (2 vCPU, 8 GB RAM) for validators, 1x e2-micro (2 vCPU, 1 GB RAM, free tier) for bootnode/RPC |
| Disk | 30 GB Standard SSD per VM |
| OS | Debian 11 |
| Region | us-central1 |
| **Virtual Private Cloud** | 1 VPC, 1 Subnetwork in us-central1 |
| Firewall | Allow TCP:8545 (RPC), TCP:30303 (P2P) |
| Egress | ~5 GB/month |
| **Cloud Storage** | Bucket: 50 GB Standard Storage |
| Region | us-central1 |
| **Cloud KMS** | 1 Key Ring, 3 Symmetric Keys (1 per validator) |
| Region | global |
| **Cloud Monitoring** | Enable Monitoring API |
| Metrics | ~50 MB/month (CPU, memory, TPS) |
| API Requests | ~5,000 ListMetrics/GetMetricData |
| **Grafana/Prometheus** | VM: Shared with bootnode (e2-micro) |
| Disk | 10 GB Standard SSD |
| Docker | grafana/grafana, prom/prometheus |
| **Cloud Pub/Sub** (Optional) | 1 Topic, 1 Subscription |
| Data | ~5 GB/month |
| Messages | ~50,000/month |

## Optimal Unconstrained Configuration

This configuration ensures high performance, scalability, and reliability without restrictions, suitable for production-grade networks (e.g., 5+ validators) with high transaction volumes or critical applications.

| Resource Type         | Configuration Details |
|-----------------------|-----------------------|
| **Compute Engine (GCE)** | 6 VMs: 4x e2-standard-4 (4 vCPU, 16 GB RAM) for validators, 1x e2-standard-4 for RPC node, 1x e2-standard-4 for bootnode |
| Disk | 100 GB Standard SSD per VM |
| OS | Debian 11 |
| Region | us-central1 |
| **Virtual Private Cloud** | 1 VPC, 2 Subnetworks (validators, RPC/bootnode) in us-central1 |
| Firewall | Allow TCP:8545 (RPC), TCP:30303 (P2P) |
| Egress | ~20 GB/month |
| **Cloud Storage** | Bucket: 200 GB Standard Storage, lifecycle rule to Coldline after 30 days |
| Region | us-central1 |
| **Cloud KMS** | 1 Key Ring, 5 Symmetric Keys (1 per validator + 1 spare) |
| Region | global |
| **Cloud Monitoring** | Enable Monitoring API |
| Metrics | ~200 MB/month (CPU, memory, TPS, network) |
| API Requests | ~20,000 ListMetrics/GetMetricData |
| **Grafana/Prometheus** | VM: 1x e2-standard-2 (2 vCPU, 8 GB RAM) |
| Disk | 50 GB Standard SSD |
| Docker | grafana/grafana, prom/prometheus, grafana/loki |
| **Cloud Pub/Sub** (Optional) | 2 Topics, 2 Subscriptions |
| Data | ~20 GB/month |
| Messages | ~200,000/month |

## Notes

- **Minimum Compressed**: Prioritizes cost efficiency, leveraging GCP free tier. Suitable for small networks or testing. May face performance limits with high TPS or scaling beyond 3 validators.
- **Optimal Unconstrained**: Designed for production with high TPS (1000-1500), scalability (up to 10 validators), and reliability.
- **Terraform**: Both configurations can use Terraform code, adjusted for VM types and counts.
- **Grafana**: Included in both setups for monitoring.
- **Client Calculation**: Clients can input these parameters into the Google Cloud Pricing Calculator.