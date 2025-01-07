# AutoPxEth Audit (2025-01-07)

## Overview
This repository contains the results of a comprehensive security audit of the `AutoPxEth.sol` contract, performed on January 7, 2025.

### Structure
- **contracts/**: The target contract (`AutoPxEth.sol`) and exploit contracts for PoC.
- **test/**: Foundry tests for validating vulnerabilities.
- **audit-report/**: The full audit report and findings summary.
- **foundry.toml**: Foundry configuration file.

### Findings Summary
| Severity  | Count |
|-----------|-------|
| Critical  | 1     |
| High      | 2     |
| Medium    | 3     |
| Low       | 4     |

## Running Tests
1. **Install Foundry**:
   ```bash
   curl -L https://foundry.paradigm.xyz | bash
   foundryup
