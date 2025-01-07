Security Review for AutoPxEth.sol
Prepared by: Felcopentesting
Date: 2025/01/07

1. Executive Summary
The security review of the AutoPxEth.sol contract revealed the following findings:

Critical Risk: 1
High Risk: 2
Medium Risk: 3
Low Risk: 4

The identified vulnerabilities include improper access control, unchecked usage of external libraries, and potential exploits due to the absence of robust validation checks. These issues could lead to unauthorized token transfers, protocol mismanagement, or partial loss of funds.

2. Risk Classification
Severity	Count	Description
Critical	1	    Exploitable flaws that can lead to complete loss of protocol funds or critical asset mismanagement.
High	    2	    Severe issues that impact protocol functionality or user funds significantly.
Medium	    3	    Vulnerabilities that cause partial loss or inefficiency but do not critically compromise the protocol.
Low	        4	    Minor issues that affect usability, gas efficiency, or edge-case scenarios.


3. Findings
3.1 Critical Risk
3.1.1 Centralized Control Over Critical Functions

Location: setPlatform, setPirexEth, notifyRewardAmount.
Description: The owner role controls critical protocol functions, allowing potential misuse, such as redirection of fees or replacement of core contracts.
Impact: Unauthorized changes to the platform could lead to protocol mismanagement or theft.
Recommendation: Implement governance mechanisms (e.g., DAO, multisig) and timelocks for critical updates.

3.2 High Risk
3.2.1 Arbitrary transferFrom Exploit

Location: transferFrom in AutoPxEth.sol.
Description: Unauthorized token transfers are possible due to insufficient validation of the from address in transferFrom.
Impact: Attackers can steal funds from users with approved allowances.
PoC: See Section 4.
Recommendation: Restrict msg.sender in transferFrom to the token owner or approved operator.


3.2.2 SafeTransferLib Token Existence Check

Location: SafeTransferLib usage in AutoPxEth.sol.
Description: Solmate's SafeTransferLib does not verify token contract existence, leading to unpredictable behavior with non-existent tokens.
Impact: Calls with non-existent tokens could fail silently or misbehave.
PoC: See Section 4.
Recommendation: Replace SafeTransferLib with OpenZeppelin's SafeERC20, which includes contract existence checks.

3.3 Medium Risk
3.3.1 Event Transparency

Location: Missing events in critical state changes like beforeWithdraw and afterDeposit.
Description: Lack of emitted events for significant actions reduces visibility for off-chain monitoring.
Impact: Hinders audit trails and transparency.
Recommendation: Emit events for all critical state changes.

3.3.2 Reward Mismanagement

Location: harvest.
Description: Fee miscalculations or reward over-distribution may occur due to improper handling of state variables.
Impact: Misallocation of protocol rewards or user funds.
Recommendation: Add comprehensive unit tests and validations for reward calculations.

3.3.3 Gas Inefficiency

Location: harvest, rewardPerToken.
Description: Repeated reads of state variables increase gas costs.
Impact: Higher operational expenses for users.
Recommendation: Cache frequently accessed state variables.

3.4 Low Risk
3.4.1 Documentation Gaps

Description: Inline comments and function-level documentation are incomplete.
Recommendation: Add detailed comments explaining function behavior and state variables.

3.4.2 Ownership Transfer

Location: AutoPxEth.sol.
Description: Use of single-step ownership transfer is risky.
Recommendation: Switch to OpenZeppelin's Ownable2Step for safer ownership transfers.

4. Proof of Concept (PoC)
4.1 Arbitrary transferFrom Exploit

Steps:

Deploy the AutoPxEth contract.
Deploy a mock ERC20 token and approve an allowance for the victim account.
Use an exploit contract to call transferFrom with the victim's address.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

contract ExploitTransferFrom {
    function exploit(address vault, address victim, uint256 amount) external {
        vault.call(abi.encodeWithSignature("transferFrom(address,address,uint256)", victim, msg.sender, amount));
    }
}

```

4.2 SafeTransferLib Token Existence

Steps:

Deploy the AutoPxEth contract.
Use a non-existent token address and call harvest.

5. Recommendations
Governance: Transition critical roles to decentralized governance mechanisms.
Validation: Add stricter validation for transferFrom and token existence.
Efficiency: Optimize gas usage with caching techniques.
Transparency: Emit events for all significant actions.

6. Appendix
Test Setup
Foundry Test Example:

```
forge test --match-contract ExploitTransferFromTest

```