# v0.3.0 - 2025-02-25

## New modules

### Empenodes

The empenodes module manages special vesting accounts created by locking a fixed token deposit. It supports node creation, reward claiming, and parameter updates.

**Core Operations & Functionality:**

- **Create Soft Node:**  (`MsgCreateSoftNode`)
  Users create a soft node by depositing a fixed amount from their vesting account. This operation:
    - Sets up a new vesting account for the node.
    - Registers the node under the owner’s account.
    - Automatically grants the owner permissions (e.g., staking, redelegation, reward withdrawal, and voting).

- **Claim Rewards:**  (`MsgClaimRewards`)
  Soft node owners can claim tokens from their node’s account. The claimable amount depends on timing:
    - **After Full Reward Time:** All available tokens are released.
    - **During Penalty Period:** Only a fraction (based on a penalty multiplier) is claimable; the remainder is burned.

- **Update Parameters:**  (`MsgUpdateParams`)
  Authorized governance can adjust module settings to fine-tune node behavior.

**Parameters:**

- **Node Types:**  
  Each node type defines:
    - **Deposit Amount:** The fixed token amount required.
    - **Lockup & Claim Durations:** How long tokens are locked, plus timings for when claims are allowed (with and without penalties).
    - **Penalty Multiplier:** The fraction of tokens claimable during the penalty period.

- **Burn Address:**  
  Tokens not claimable due to penalties are sent to this address.

### Stablefee
The stablefee module manages fee configurations on the blockchain by converting fees defined in a stable currency (e.g., USDT)
into the native chain currency (EMPE) using a dynamic exchange rate. It supports updating the exchange rate and modifying fee parameters via governance.

**Core Operations & Functionality:**

- **Exchange Rate Management:**  (`MsgSetExchangeRate`)  
  Authorized providers update the exchange rate between the stable currency and EMPE. This operation:
    - Verifies the sender is authorized.
    - Updates the exchange rate in the module’s state.

- **Update Parameters:**  (`MsgUpdateParams`)  
  Governance can adjust fee settings to fine-tune the fee mechanism.

**Parameters:**

- **Base Currency:**  
  The stable currency identifier (e.g., "USDT") used for fee calculations.

- **Fee Pairs:**  
  A list mapping operations (e.g., "deactivateDidDocument") to fee amounts in the stable currency.

- **Fee Distributions:**  
  A list specifying how collected fees are split among recipients via assigned fractions.

- **Exchange Rate Providers:**  
  Authorized addresses that are permitted to update the exchange rate.

### New Module: WASM
The WASM module enables the execution of WebAssembly (WASM) smart contracts on the blockchain. It provides the ability to deploy,
instantiate, execute, and migrate smart contracts. Users interact with the module via a set of specialized messages,
and the module exposes queries to retrieve detailed information about stored code, contract instances, and their state.

**Core Operations & Functionality:**

- **Store Code:**  (`MsgStoreCode`)  
  Users upload and register WASM bytecode on-chain. This operation:
    - Compiles and validates the WASM code.
    - Stores the compiled code along with its checksum for future instantiation.
    - Sets access permissions to control who is allowed to instantiate contracts from this code.

- **Instantiate Contract:**  (`MsgInstantiateContract`)  
  Users create a new smart contract instance from stored WASM code

- **Execute Contract:**  (`MsgExecuteContract`)  
  Contract users can trigger the execution of a smart contract’s functionality. This operation sends a JSON payload to the contract containing the execution command.

**Parameters:**

- **Code Upload Access:**  
  Defines which addresses are permitted to upload WASM code. This can be configured to allow uploads from everyone or restrict uploads to a specified list of addresses.

- **Instantiate Default Permission:**  
  Determines the default access level for instantiating contracts from stored code. Options include allowing everyone, nobody, or only specific addresses.

## Improvements

### DID Document Module Enhancements
- New params:
    - **DidDocTiers**: A list of tiers that set limits for DID documents. Each tier includes:
        - **Name**: The tier's name (e.g. "default", "tier1").
        - **MaxDidVerificationMethodsCount**: The maximum number of verification methods (keys) allowed.
        - **MaxDidElementsCount**: The maximum number of additional elements (e.g. context, controller, service) permitted in the document.
- When creating a DID document, the user selects a tier, and the document is validated to ensure it does not exceed the limits defined for that tier. This also influences the fees applied for the operation.
- Implemented new compression and decompression methods in the `x/diddoc` module to reduce the size of DID documents.
- Improved query handling in the `x/diddoc` module with better parameter parsing and validation.
- Integrated fee mechanisms for creating, updating, and deactivating DID documents.
- Simplified the DID document update process by removing the old update method and replacing it with a single update method that handles all changes.
  This change is a breaking change and requires users to update their operations accordingly. This method also includes a fee mechanism.

### Code and binary improvements

- Implemented custom antehandler to support cosmwasm mechanics
- Moved `signer` functionality to `emped` binary
- Implemented an automatic devnet script that creates a development network with a single command, making testing more convenient.

## Bug Fixes

- Fixed swagger api and swagger generation
- Fixed ignite cli chain serve command
- Fixed all simulation tests and extended the test cases to cover additional edge scenarios.

## Breaking changes

- The old update method in the `x/diddoc` module is deprecated.
- Creation of DID documents now requires selecting a tier.
- The introduction of fees in the `x/diddoc` module may require users to adjust their operations to account for these fees.
- Endpoints used for querying empe modules were changed to reflect the new module structure and versioning.
- Signer functionality was moved from the `emped` binary to the `emped` module, which may require users to update their scripts or applications to reflect this change.


## Tests Carried Out

- Simulation tests
- Performance/stability tests
- Manual E2E tests
- Automatic E2E tests
- Unit tests
- Automatic and manual migration tests


