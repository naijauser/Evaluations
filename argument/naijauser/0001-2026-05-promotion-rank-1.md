# Argument-0001: Promotion to Rank 1

|                 |                                                                                             |
| --------------- | ------------------------------------------------------------------------------------------- |
| **Report Date** | Date of submission (2026/05/29)                                                             |
| **Submitted by**| naijauser                                                                       |


## Member details

- Matrix username: @naijauser:matrix.org
- Polkadot address: 12mzp6SdXsT9NbCwYptcTNeALheiaBVRSM7XpCb4Z3HP9wLP
- Current rank: Candidate
- Date of initial induction: 2026/05/19
- Date of last report: N/A
- Area(s) of Expertise/Interest: FRAME, Tooling, Network, Virtual Machine


## Reporting period

- Start date: 2025/11/01
- End date: 2026/05/29


## Argument
I have been actively contributing to the Polkadot ecosystem since November 2025. In that time I have contributed across three repositories — the core Polkadot-SDK, Polkadot Ecosystem Tests, and Try Runtime CLI.

For promotion to Rank 1, the Manifesto states as a requirement:
>Three clear examples of a modest but substantial contribution to protocol development

My argument presents contributions across all three repositories. Merged PRs are noted as such; open PRs reflect active, ongoing work.

### Polkadot Ecosystem Tests
#### 1. Deepen Governance Pallet Conviction Voting Test Coverage (merged)
[polkadot-ecosystem-tests#556](https://github.com/open-web3-stack/polkadot-ecosystem-tests/pull/556) deepens test coverage for the governance pallet. It covers voting (standard with conviction, split, split+abstain), vote removal, voting account lock and freezes before and after voting, and vote delegation.

**Impact:** Ensures the conviction voting workflow — which directly determines governance outcomes and token locks — is integration-tested end-to-end, catching regressions in the critical path for on-chain governance.

#### 2. Add Test Coverage for Configuration Pallet (merged)
[polkadot-ecosystem-tests#563](https://github.com/open-web3-stack/polkadot-ecosystem-tests/pull/563) adds test coverage for the configuration pallet. It covers all configuration options including Core Configuration, Scheduler Configuration, Dispute Configuration, Message Queue Configuration, and HRMP Configuration.

**Impact:** Ensures the configuration pallet's full surface area is integration-tested, catching regressions in relay chain operational parameters before they reach production.

#### 3. Add Test Coverage for Registrar Pallet (merged)
[polkadot-ecosystem-tests#572](https://github.com/open-web3-stack/polkadot-ecosystem-tests/pull/572) adds test coverage for the registrar pallet. It covers Registration Functions (reserve, register, force_register, deregister) and Lifecycle Management functions (swap, add_lock, remove_lock, schedule_code_upgrade, set_current_head).

**Impact:** Ensures parachain registration and lifecycle management are tested end-to-end, catching regressions in the critical path for onboarding and managing parachains on the relay chain.

#### 4. Add Test Coverage for Asset Rate Pallet (merged)
[polkadot-ecosystem-tests#576](https://github.com/open-web3-stack/polkadot-ecosystem-tests/pull/576) adds test coverage for the asset rate pallet. It covers key scenarios of creating a new asset rate, updating an existing asset rate, removing an asset rate, and ensuring that only authorized origins can create, update, or remove rates.

**Impact:** Verifies authorization enforcement for asset rate management, ensuring only privileged origins can modify rates that affect cross-chain asset valuations.

#### 5. Deepen Registrar Pallet Error Path and Edge Case Coverage (open)
[polkadot-ecosystem-tests#633](https://github.com/open-web3-stack/polkadot-ecosystem-tests/pull/633) expands registrar pallet test coverage to include error conditions and boundary cases missing from the earlier PR. It covers insufficient balance and invalid validation code/genesis head sizes during registration, deregistration of non-existent parachains, double-lock prevention, lock removal restrictions, swap authorization (root and para origins), and code upgrade/head update permissions on locked or non-existent parachains.

**Impact:** Closes the gap between happy-path registrar coverage and production reality by testing the error paths that guard against invalid inputs and unauthorized operations — the cases most likely to be exploited or regressed.

### Polkadot-SDK
#### 1. Add support for asset V3 to V5 conversion of LocalPay (merged)
[polkadot-sdk#10657](https://github.com/paritytech/polkadot-sdk/pull/10657) adds V3→V5 asset conversion support to the `match_asset` function in `LocalPay`. The function previously only handled V4 and V5 assets — clients still encoding assets as V3 would fail asset matching entirely, blocking fee payment. The fix adds a V3→V5 conversion path (via V4), restoring compatibility for legacy clients without breaking existing behaviour.

**Impact:** Unblocks fee payment for runtimes using `LocalPay` where the sender encodes assets as V3, preventing silent transaction failures at the XCM version boundary.

#### 2. Run PVF worker security checks at node startup (open)
[polkadot-sdk#10386](https://github.com/paritytech/polkadot-sdk/pull/10386) moves PVF worker security checks to run at node startup rather than only when a worker is first used. Previously, the node verified worker binary versions on startup but deferred all other integrity checks until the worker was actually invoked — which only happens when the node is in the active validator set. The fix invokes the full set of checks at startup and halts the node immediately if any fail, so operators are informed before the node is ever scheduled to validate.

**Impact:** Eliminates a silent failure window where a validator node could run with broken worker binaries undetected, protecting both operator rewards and broader network performance.

#### 3. Add dedicated BenchmarkConfig trait to FRAME (open)
[polkadot-sdk#10806](https://github.com/paritytech/polkadot-sdk/pull/10806) introduces a dedicated `BenchmarkConfig` trait that extends `pallet::Config` and houses benchmarking-specific helpers separately from the main pallet configuration. Previously, benchmarking logic had to be embedded directly in the `Config` trait behind a `runtime-benchmarks` feature flag, cluttering production configuration with test-only concerns. The PR demonstrates the pattern with an example pallet where runtimes implement `BenchmarkConfig` independently, supplying a `BenchmarkHelper` type without touching the core `Config` trait.

**Impact:** Cleanly separates benchmarking configuration from production pallet configuration, reducing feature-flag noise in `Config` and making it easier to add complex benchmark setup logic without modifying the main trait.

#### 4. Add try-state checks to the timestamp pallet (open)
[polkadot-sdk#10426](https://github.com/paritytech/polkadot-sdk/pull/10426) adds `do_try_state` to the timestamp pallet, implementing the try-state invariant checking pattern for pallet state validation. Try-state checks verify that a pallet's on-chain state is consistent — analogous to checking that the sum of all balances in `pallet-balances` equals the recorded total issuance. The PR adds the validation logic, integrates the `ensure` macro, and updates the test suite to cover the new checks.

**Impact:** Brings the timestamp pallet into the try-state framework, enabling runtime developers and upgrade tooling to catch state inconsistencies in this pallet before they propagate into production.

#### 5. Remove `pallet::getter` usage from sassafras pallet (merged)
[polkadot-sdk#10460](https://github.com/paritytech/polkadot-sdk/pull/10460) removes `pallet::getter` from the sassafras pallet and replaces it with the preferred syntax `Key::<T>::get()`. The `pallet::getter` macro generates auto-named getter functions that are being phased out across the SDK in favour of explicit storage access, which is less error-prone and reduces macro expansion overhead.

**Impact:** Advances the ecosystem-wide removal of `pallet::getter`, reducing macro expansion overhead and aligning the sassafras pallet with the modern FRAME storage access convention.

#### 6. Remove `pallet::getter` usage from Merkle Mountain Range pallet (merged)
[polkadot-sdk#10437](https://github.com/paritytech/polkadot-sdk/pull/10437) removes `pallet::getter` from the Merkle Mountain Range pallet and replaces it with the preferred syntax `Key::<T>::get()`.

**Impact:** Advances the ecosystem-wide removal of `pallet::getter`, reducing macro expansion overhead and aligning the MMR pallet with the modern FRAME storage access convention.

#### 7. Remove `pallet::getter` usage from snowbridge pallets (open)
[polkadot-sdk#10467](https://github.com/paritytech/polkadot-sdk/pull/10467) removes `pallet::getter` from the snowbridge pallets and replaces it with the preferred syntax `Key::<T>::get()`. The `pallet::getter` macro generates auto-named getter functions that are being phased out across the SDK in favour of explicit storage access, which is less error-prone and reduces macro expansion overhead.

**Impact:** Advances the ecosystem-wide removal of `pallet::getter` into the snowbridge pallets, reducing macro expansion overhead and aligning them with the modern FRAME storage access convention.

### Try Runtime CLI
#### 1. Execute a range of past blocks (open)
[try-runtime-cli#140](https://github.com/paritytech/try-runtime-cli/pull/140) adds `from` and `to` arguments to the CLI, enabling execution of a sequential range of historical blocks rather than only individual blocks. The implementation extracts block execution into a reusable helper, loops through the specified range fetching and processing each block hash in order, and maintains state across executions. Test coverage for the new range execution capability is included.

**Impact:** Enables operators and developers to replay a span of historical blocks in one command, making it significantly easier to reproduce and debug state transitions that span multiple blocks.

#### 2. Add tests for offchain-worker command (open)
[try-runtime-cli#139](https://github.com/paritytech/try-runtime-cli/pull/139) adds test coverage for the `offchain-worker` command, which previously had no automated tests. The PR implements a happy-path test validating successful offchain worker execution and an error-state test validating failure handling.

**Impact:** Establishes a test baseline for the offchain-worker command, reducing the risk of regressions in a component that was previously untested.

My contributions reflect a deliberate effort to build depth across the protocol stack. A full picture of my contributions across Polkadot-SDK, Polkadot Ecosystem Tests, and Try Runtime CLI can be found below.

- [polkadot-sdk:@naijauser](https://github.com/paritytech/polkadot-sdk/pulls/naijauser)
- [polkadot-ecosystem-tests:@naijauser](https://github.com/open-web3-stack/polkadot-ecosystem-tests/pulls?q=is%3Apr+author%3Anaijauser+)
- [try-runtime-cli:@naijauser](https://github.com/paritytech/try-runtime-cli/pulls/naijauser)

## Acknowledgement
Thanks to [Alexandre R. Baldé](https://github.com/rockbmb) for their thorough review and feedback on my PRs.

## Voting record
N/A
