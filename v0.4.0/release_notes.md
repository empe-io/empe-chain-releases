## [v0.4.0](https://github.com/empe-io/empe-chain/releases/tag/v0.4.0) - 2025-05-21

## New modules

### LinkedResources

The linkedresources module enables accounts to attach, update, and remove arbitrary binary data (“linked resources”)
to an on-chain Decentralised Identifier (DID) document. It supports resource creation, versioned updates, deletions,
and on-chain parameter governance.

**Core Operations & Functionality:**

* **Create Linked Resource:**  (`MsgCreateLinkedResource`)
  Users attach a new binary payload (up to a configurable size) to their DID. This operation:
  * Validates the DID exists and is active.
  * Verifies the payload’s `id`, `type`, and `data` length against module parameters.
  * Confirms a valid signature from a DID `capabilityInvocation` key.
  * Stores the resource at `version = 1` with status `ACTIVE`.
  * Emits `EventCreateLinkedResource` and charges the `createLinkedResource_<tier>` fee.

* **Update Linked Resource:**  (`MsgUpdateLinkedResource`)
  Resource owners submit a new version of an existing link. This operation:
  * Ensures the resource exists and is `ACTIVE`.
  * Requires the submitted `version = stored.version + 1`.
  * Validates size/type limits and signature authorization.
  * Overwrites the on-chain data, updates `updated_at`, retains `status = ACTIVE`.
  * Emits `EventUpdateLinkedResource` and charges the `updateLinkedResource_<tier>` fee.

* **Delete Linked Resource:**  (`MsgDeleteLinkedResource`)
  Owners mark a resource as deleted. This operation:
  * Checks the resource is `ACTIVE` and payload `version` matches current.
  * Verifies the signature from a `capabilityInvocation` method.
  * Zeroes out `data`, sets `status = DELETED`, updates timestamp.
  * Emits `EventDeleteLinkedResource` and charges the `deleteLinkedResource_<tier>` fee.

* **Update Parameters:**  (`MsgUpdateParams`)
  The designated authority (governance) can adjust module limits.

**Parameters:**

* **ResourceDataMaxSize:** Maximum number of bytes allowed in the resource’s `data` field.
* **ResourceIdMaxLength:** Maximum length (in characters) allowed for the resource `id`.
* **ResourceTypeMaxLength:** Maximum length (in characters) allowed for the resource `type` string.

## Dependency Upgrades

* **CometBFT:** v0.37.5 → v0.37.6
* **cometbft-db:** v0.10.0 → v0.11.0
* **Cosmos SDK:** v0.47.15 → v0.47.17
* **IBC-Go:** v7.4.0 → v7.10.0
* **Go:** v1.22 → v1.23
* Other minor dependencies were also updated as needed to accommodate these core upgrades.

## Bug fixes
* Fixed nondeterministic behavior in the `x/diddoc` module simulations.

## Tests Carried Out

* Simulation tests
* Performance/stability tests
* Manual E2E tests
* Automatic E2E tests
* Unit tests
* Automatic and manual migration tests