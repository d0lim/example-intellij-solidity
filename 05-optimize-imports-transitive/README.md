# Scenario 5: Optimize Imports — No Duplicate Transitive Imports

**Tests:** Running Optimize Imports does not add a redundant transitive import when the same-named symbol exists at multiple paths.

## File Structure

```
├── remappings.txt                      # (empty)
├── openzeppelin/
│   ├── upgrades-core/contracts/Initializable.sol        # Initializable (transitive)
│   └── contracts-upgradeable/
│       ├── proxy/utils/Initializable.sol                # Initializable (direct import)
│       └── access/OwnableUpgradeable.sol
├── src/MyCoin.sol
└── README.md
```

## The Problem (Issue #453)

`Initializable` exists at **two paths**:
- `contracts-upgradeable/proxy/utils/Initializable.sol` — directly imported
- `upgrades-core/contracts/Initializable.sol` — reachable transitively (the deprecated path)

Before the fix, Optimize Imports would add a **second** `import {Initializable}` from the transitive path, causing a duplicate import and build failure.

## Test Procedure

1. Open this directory as a project in IntelliJ with the plugin installed
2. Open `src/MyCoin.sol`
3. Run Optimize Imports (Ctrl+Alt+O / Cmd+Alt+O on macOS)
4. **Expected:** Exactly 2 import statements remain:
   - `import "../openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";`
   - `import "../openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";`
5. **Expected:** `upgrades-core/contracts/Initializable.sol` is **NOT** added as a duplicate import
6. Run Optimize Imports again
7. **Expected:** Imports remain stable (no oscillation)

## Related

- [PR #2](https://github.com/d0lim/intellij-solidity/pull/2)
- [Issue #453](https://github.com/intellij-solidity/intellij-solidity/issues/453)
