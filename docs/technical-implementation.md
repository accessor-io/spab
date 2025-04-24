# Technical Implementation

## On-Chain Attestation Architecture

The ENS Service Provider Advisory Board implements a hierarchical, on-chain attestation system that provides verifiable, immutable records of service provider performance metrics. This document outlines the technical architecture and implementation details of the system.

### System Architecture

The attestation framework consists of four distinct contract layers:

1. **Service Provider Contracts (Layer 1)**: 
   - Standardized contracts deployed by service providers
   - Emit structured events compliant with EIP-712 for key operations
   - Implement required interfaces for metrics reporting

2. **Metrics Aggregation Layer (Layer 2)**:
   - Collects and processes events from service provider contracts
   - Computes derived performance metrics (e.g., Expense Rate)
   - Initiates attestation requests to EAS

3. **Attestation & Registry Layer (Layer 3)**:
   - Interfaces with Ethereum Attestation Service (EAS)
   - Maintains the canonical registry of providers
   - Links providers to their attestation records

4. **Governance Layer (Layer 4)**:
   - Controls parameter updates and system configuration
   - Provides manual attestation capabilities
   - Implements multi-signature board governance

### Data Flow Mechanics

The system operates through a deterministic event-driven architecture:

1. Service provider contracts emit standardized events:
   - `SubnameMinted(address indexed owner, string name, uint256 timestamp)`
   - `ResolutionProcessed(string name, bytes result, uint256 timestamp)`
   - `BurnRateUpdated(uint256 newRate, uint256 timestamp)`

2. The Metrics Aggregator:
   - Consumes these events to update provider metrics
   - Maintains cumulative and time-series data
   - Calculates derived metrics such as Expense Rate (burnRate/subnamesMinted)

3. At predetermined intervals or thresholds:
   - The Metrics Aggregator packages performance data
   - Submits an attestation request to EAS
   - Receives an attestation ID that is stored in the Registry

4. The Service Provider Registry:
   - Maintains the official list of approved providers
   - Links each provider to their attestation history
   - Tracks additional metadata (tier, status, version)

### Smart Contract Integration

#### Service Provider Interface

All ENS service providers must implement the `IServiceProvider` interface: 