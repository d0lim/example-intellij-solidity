# Design: E2E Test Example Solidity Projects

**Date:** 2026-02-26
**Related PR:** https://github.com/d0lim/intellij-solidity/pull/2
**Purpose:** Create example Solidity projects to manually verify the E2E Test Plan from PR #2, which fixes `remappings.txt` resolution with arbitrary target paths (#458).

## Decisions

- **6 independent projects** — one per E2E scenario, numbered for ordering
- **Minimal skeleton Solidity** — empty contract declarations + import statements only
- **Each project includes a README** — test procedure, expected results, related PR info
- **Flat directory structure** — `01-xxx/`, `02-xxx/`, etc. under the repo root

## Project Structure

```
example-intellij-solidity/
├── README.md                          # Overall overview, PR link, usage instructions
├── 01-node-modules-remapping/
├── 02-no-trailing-slash-remapping/
├── 03-foundry-toml-remapping/
├── 04-custom-target-dir-remapping/
├── 05-optimize-imports-transitive/
└── 06-mixed-remapping-sources/
```

## Scenario Details

### 1. `01-node-modules-remapping/` — Issue #458 Core Scenario

Tests remappings pointing to `node_modules/` targets.

```
├── remappings.txt          # @chainlink=node_modules/@chainlink/contracts
├── node_modules/@chainlink/contracts/src/Token.sol
├── src/Main.sol
└── README.md
```

- **remappings.txt:** `@chainlink=node_modules/@chainlink/contracts`
- **Token.sol:** `contract Token {}`
- **Main.sol:** Empty contract; tester types `Token` and triggers auto-import
- **Expected:** `import "@chainlink/src/Token.sol";` resolves correctly

### 2. `02-no-trailing-slash-remapping/` — Trailing Slash Normalization

Tests remappings without trailing slash.

```
├── remappings.txt          # @oz=lib/openzeppelin  (no trailing slash)
├── lib/openzeppelin/token/ERC20.sol
├── src/Main.sol
└── README.md
```

- **remappings.txt:** `@oz=lib/openzeppelin` (no trailing slash — key test case)
- **ERC20.sol:** `contract ERC20 {}`
- **Expected:** `import "@oz/token/ERC20.sol";`

### 3. `03-foundry-toml-remapping/` — foundry.toml Remappings

Tests remappings defined in `foundry.toml` instead of `remappings.txt`.

```
├── foundry.toml            # [profile.default] remappings = ["forge-std/=lib/forge-std/src/"]
├── lib/forge-std/src/Test.sol
├── src/Main.sol
└── README.md
```

- **foundry.toml:** `remappings = ["forge-std/=lib/forge-std/src/"]`
- **Test.sol:** `contract Test {}`
- **Expected:** `import "forge-std/Test.sol";`

### 4. `04-custom-target-dir-remapping/` — Soldeer-Style Custom Targets

Tests remappings to non-standard target directories.

```
├── remappings.txt          # @deps/=dependencies/@deps/
├── dependencies/@deps/token/ERC20.sol
├── src/Main.sol
└── README.md
```

- **remappings.txt:** `@deps/=dependencies/@deps/`
- **Expected:** `import "@deps/token/ERC20.sol";`

### 5. `05-optimize-imports-transitive/` — No Duplicate Transitive Imports

Tests that Optimize Imports does not add redundant transitive imports.

```
├── remappings.txt          # (empty or minimal)
├── src/A.sol               # import "./B.sol"; contract A is Initializable {}
├── src/B.sol               # import "./C.sol"; contract B {}
├── src/C.sol               # contract Initializable {}
└── README.md
```

- **A.sol:** imports B.sol, uses `Initializable` (defined in C.sol, reachable via B→C)
- **Expected:** Optimize Imports does NOT add `import "./C.sol";` to A.sol
- **Expected:** Repeated optimize keeps imports stable (no oscillation)

### 6. `06-mixed-remapping-sources/` — Combined remappings.txt + foundry.toml

Tests that remappings from both sources work simultaneously.

```
├── remappings.txt          # @utils=lib/utils
├── foundry.toml            # [profile.default] remappings = ["@core/=lib/core/"]
├── lib/utils/Helper.sol
├── lib/core/Base.sol
├── src/Main.sol
└── README.md
```

- **remappings.txt:** `@utils=lib/utils`
- **foundry.toml:** `remappings = ["@core/=lib/core/"]`
- **Expected:** `import "@utils/Helper.sol";` and `import "@core/Base.sol";`

## Solidity File Convention

All `.sol` files use:
- `// SPDX-License-Identifier: MIT`
- `pragma solidity ^0.8.0;`
- Minimal empty contract declarations (just `contract Name {}`)
- `src/Main.sol` in each project is the entry point for testing

## README Convention

Each project's README includes:
1. Scenario description and what it tests
2. File structure overview
3. Step-by-step test procedure (open in IntelliJ, trigger auto-import, etc.)
4. Expected results
5. Link to PR #2
