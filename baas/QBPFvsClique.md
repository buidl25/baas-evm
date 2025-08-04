# QBFT vs Clique Consensus Comparison

## Performance Metrics

### Block Time Comparison

**Clique (Hyperledger Besu):**
 
  - Faster block creation (single-round consensus)
  - Typical block time: 5-15 seconds (configurable in genesis file)
  - Example: With 3 validators, stable 5-second block time

**QBFT/IBFT (GoQuorum):**
 
  - Slower due to 3-phase consensus (Pre-Prepare, Prepare, Commit)
  - Typical block time: 5-20 seconds (increases with more validators)
  - Example: With 10 validators, block time may increase to 15-20 seconds

*Conclusion:* Clique creates blocks faster, especially in small networks (3-5 validators)

### Transaction Throughput (TPS)

**Clique:**
 
  - Higher TPS due to fewer communication rounds
  - Example (Hyperledger Caliper test on Besu 22.4):
    - 3 validators: 1000-1500 TPS for simple token transfers

**QBFT/IBFT:**
 
  - Lower TPS due to additional consensus rounds
  - Example (same test):
    - 4 validators: QBFT 500-800 TPS, IBFT 400-600 TPS
    - Scales to 14 validators without significant performance loss

*Conclusion:* Clique provides higher TPS in small networks, QBFT better for large networks

### Scalability with Validator Count

**Clique:**
 
  - Block time remains stable but fork probability increases
  - Example: With 10 validators, more chain reorganizations occur

**QBFT:**
 
  - Block time increases with more validators
  - More stable than IBFT at scale (up to 14 validators)
  - Example: 14 validators may have 20-second block times

*Conclusion:* Clique better for small networks, QBFT better for large networks

For more detailed comparison, see [Consensus Protocol Comparison](https://docs.catalyst.intellecteu.com/besu/Consensus%20Protocols/choose-a-cp#comparison-summary)

## Comparison Table

| Metric               | Clique (Besu)       | QBFT (GoQuorum)     | IBFT (GoQuorum)     |
|----------------------|---------------------|---------------------|---------------------|
| TPS (3-4 validators) | 1000-1500           | 500-800             | 400-600             |
| Block Time           | 5-10s               | 10-20s              | 10-25s              |
| Max Validators       | &lt;10 (stable)     | 14 (stable)         | 10 (less efficient) |
