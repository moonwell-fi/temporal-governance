# TemporalGovernor Smart Contract Documentation

## Overview

The TemporalGovernor smart contract is a versatile governance and execution layer designed to be seamlessly integrated into blockchain projects. It leverages the Wormhole bridge as a trusted source of truth for executing actions across different chains. This contract is built to ensure the integrity and security of projects under specific assumptions and conditions.

**Important Assumptions**:
1. The Wormhole bridge is secure and will not send malicious messages or be deactivated.
2. The connected blockchain (referred to as "Base") is secure.
3. Governance on the Base blockchain cannot be compromised.

Should these assumptions be violated, the contract's functionality may be affected. However, the contract incorporates mechanisms to address potential issues arising from these assumptions.

### Contract Features

- **Governance**: The contract supports a flexible governance system where trusted addresses on the connected blockchain can propose and execute actions.
- **Trusted Senders**: Trusted addresses from various chains can send messages to this contract. These trusted senders are customizable and updatable by governance.
- **Proposal Queue**: The contract maintains a queue of proposals to ensure actions are executed only once and within specified time limits.
- **Pause Mechanism**: It allows the contract to be paused by designated guardians or governance in cases of emergencies or security concerns.
- **Guardian Revocation**: The guardian's ability to pause the contract can be revoked, and governance can regain control. This can also unpause the contract if it was paused.
- **Permissionless Unpausing**: After a certain period, the contract can be unpause automatically without requiring guardian intervention, providing a safeguard against potential malicious actions by the contract owner.

### Other Features
Automation scripts are included to facilitate the use of this contract. These scripts can be used to automatically queue and execute proposals, as well as manually queue proposals for future execution. These scripts are designed to work with [OpenZeppelin Defender](https://www.openzeppelin.com/defender). You should be able to fully automate cross-chain proposal execution between two Ethereum networks with the "free" tier of Defender.

- **Queue Proposal**: [Code](/queue-vaa/src/queue.js) is included to automatically queue a proposal for later execution by detecting the `LogMessagePublished` event from the Wormhole bridge.
- **Execute Proposal**: [Code](/queue-vaa/src/execute.js) is included to automatically execute a proposal after a configurable delay (24 hours by default) from when the proposal is queued.
- **Fake Queue**: [Code](/queue-vaa/src/fake-queue.js) is included to manually schedule a queued proposal for future execution. This can be useful for testing purposes, or in the event that a proposal was not automatically queued.

**Note**: The automation scripts are not required for the contract to function. They are provided as a quality of life improvement for your community, so that you don't need to manually queue and execute cross-chain proposals.

## Contract Details

### Contract Addresses and Immutables

- `wormholeBridge`: A reference to the Wormhole bridge contract.
- `proposalDelay`: The duration a proposal must wait before processing.
- `permissionlessUnpauseTime`: The duration until the contract can be unpause permissionlessly.

### Storage Slots

- `lastPauseTime`: Timestamp of the last pause operation.
- `guardianPauseAllowed`: A flag indicating whether guardians can pause the contract.
- `trustedSenders`: A mapping of chain IDs to a set of trusted sender addresses.
- `queuedTransactions`: A mapping of proposal hashes to proposal information.

## Contract Functions

### View-Only Functions

1. `isTrustedSender(uint16 chainId, bytes32 addr)`: Checks if an address is a trusted sender for a given chain.
2. `isTrustedSender(uint16 chainId, address addr)`: An overloaded version of `isTrustedSender`.
3. `allTrustedSenders(uint16 chainId)`: Retrieves the list of trusted senders for a given chain.

### Guardian and Governance Functions

4. `setTrustedSenders(TrustedSender[] calldata _trustedSenders)`: Updates the list of trusted senders. Callable only through a governance proposal.
5. `unSetTrustedSenders(TrustedSender[] calldata _trustedSenders)`: Removes trusted senders from the list. Callable only through a governance proposal.
6. `grantGuardiansPause()`: Grants the guardians the ability to pause the contract.
7. `changeGuardian(address newGuardian)`: Transfers the guardian role to a new address. Callable only through governance proposal.

### Guardian-Only Functions

8. `revokeGuardian()`: Revokes the guardian's ability to pause the contract. Unpauses the contract if it was paused. Callable by the old guardian or governance.

### Permissionless Functions

9. `queueProposal(bytes memory VAA)`: Queues a proposal for execution. Callable when the contract is unpaused.
10. `executeProposal(bytes memory VAA)`: Executes a queued proposal. Callable when the contract is unpaused and after a specified delay.
11. `permissionlessUnpause()`: Unpauses the contract permissionlessly after a certain time.
12. `fastTrackProposalExecution(bytes memory VAA)`: Allows the guardian to fast-track proposal execution during emergencies. Callable when the contract is paused by the guardian.
13. `togglePause()`: Allows the guardian to pause or unpause the contract. Callable by the guardian.

## Why This Contract is Important

The TemporalGovernor smart contract is crucial for any blockchain project looking to implement secure cross-chain governance and execution of actions. It can be seamlessly integrated into diverse projects, ensuring trustworthiness and functionality.

This contract offers the flexibility needed to adapt to various project requirements while safeguarding against potential threats. It allows projects to maintain control over their security and governance, even in the face of unforeseen challenges or compromised governance on connected blockchains.

In summary, the TemporalGovernor contract serves as a robust governance and execution layer that enhances the security and reliability of blockchain projects, making it an invaluable component for a wide range of applications.
