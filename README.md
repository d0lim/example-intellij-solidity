# Example Solidity Projects for intellij-solidity E2E Testing

> [한국어](./README.ko.md)

A collection of example Solidity projects for manually verifying the E2E Test Plan from
[PR #2: fix remappings.txt with arbitrary target paths](https://github.com/d0lim/intellij-solidity/pull/2).

## Background

The [intellij-solidity](https://github.com/intellij-solidity/intellij-solidity) plugin failed to resolve `remappings.txt` entries pointing to `node_modules/`, custom directories, or other non-standard targets ([#458](https://github.com/intellij-solidity/intellij-solidity/issues/458)).

Key fixes in PR #2:
- **Normalize trailing slash** in `parseRemappingsFile()` — prevents missing `/` during path concatenation
- **Integrate `preferElementsFromDirectImports()`** — prevents adding duplicate transitive imports ([#453](https://github.com/intellij-solidity/intellij-solidity/issues/453))
- **Broaden `buildImportPath()` reverse remapping** — supports custom target directories beyond `lib/`
- **Fix `buildReverseRemappings()` trailing-slash consistency** — normalizes prefix values

## Projects

| # | Directory | What It Tests |
|---|-----------|---------------|
| 1 | [`01-node-modules-remapping/`](./01-node-modules-remapping/) | Remappings pointing to `node_modules/` targets (issue #458 core) |
| 2 | [`02-no-trailing-slash-remapping/`](./02-no-trailing-slash-remapping/) | Remappings without trailing slash |
| 3 | [`03-foundry-toml-remapping/`](./03-foundry-toml-remapping/) | Remappings defined in `foundry.toml` |
| 4 | [`04-custom-target-dir-remapping/`](./04-custom-target-dir-remapping/) | Custom target directory remappings (soldeer-style) |
| 5 | [`05-optimize-imports-transitive/`](./05-optimize-imports-transitive/) | Optimize Imports does not add duplicate transitive imports |
| 6 | [`06-mixed-remapping-sources/`](./06-mixed-remapping-sources/) | Mixed remapping sources (`remappings.txt` + `foundry.toml`) |

## How to Use

### Prerequisites

- Clone the [intellij-solidity](https://github.com/d0lim/intellij-solidity) repo and check out the PR #2 branch
- JDK 17+

### Steps

1. Build the plugin and launch the test IDE:
   ```bash
   cd intellij-solidity
   git checkout fix/remappings-arbitrary-target-paths  # PR #2 branch
   ./gradlew runIde
   ```
2. In the launched IntelliJ instance, **Open as Project** one of the numbered directories
3. Follow the README inside each project for step-by-step test instructions
4. Repeat for every scenario

## E2E Test Checklist

### Scenario 1: `node_modules` Target Remapping

> `remappings.txt`: `@chainlink=node_modules/@chainlink/contracts`

- [ ] Type `Token` in `src/Main.sol` and trigger auto-import (Alt+Enter)
- [ ] Import resolves as `import "@chainlink/src/Token.sol";`
- [ ] No red underlines on the import path
- [ ] Ctrl+Click navigates to `Token.sol`

### Scenario 2: Remapping Without Trailing Slash

> `remappings.txt`: `@oz=lib/openzeppelin` (no trailing slash!)

- [ ] Type `ERC20` in `src/Main.sol` and trigger auto-import
- [ ] Import resolves as `import "@oz/token/ERC20.sol";` (not `openzeppelintoken/ERC20.sol`)
- [ ] Ctrl+Click navigates to `ERC20.sol`

### Scenario 3: `foundry.toml` Remapping

> `foundry.toml`: `remappings = ["forge-std/=lib/forge-std/src/"]` (no remappings.txt)

- [ ] Type `Test` in `src/Main.sol` and trigger auto-import
- [ ] Import resolves as `import "forge-std/Test.sol";`
- [ ] Ctrl+Click navigates to `Test.sol`

### Scenario 4: Custom Target Directory Remapping

> `remappings.txt`: `@deps/=dependencies/@deps/`

- [ ] Type `ERC20` in `src/Main.sol` and trigger auto-import
- [ ] Import resolves as `import "@deps/token/ERC20.sol";`
- [ ] Ctrl+Click navigates to `ERC20.sol`

### Scenario 5: Optimize Imports — No Duplicate Transitive Imports

> Import chain: `A.sol -> B.sol -> C.sol` (C.sol defines `Initializable`)

- [ ] Open `src/A.sol` and run Optimize Imports (Ctrl+Alt+O / Cmd+Alt+O)
- [ ] `import "./C.sol";` is **NOT** added (Initializable is already reachable via B -> C)
- [ ] Running Optimize Imports again keeps imports stable (no oscillation)

### Scenario 6: Mixed Remapping Sources

> `remappings.txt`: `@utils=lib/utils`
> `foundry.toml`: `remappings = ["@core/=lib/core/"]`

- [ ] Auto-import `Helper` in `src/Main.sol` -> `import "@utils/Helper.sol";`
- [ ] Auto-import `Base` in `src/Main.sol` -> `import "@core/Base.sol";`
- [ ] No red underlines on either import
- [ ] Ctrl+Click each import path navigates to the correct file

## Related

- **PR:** https://github.com/d0lim/intellij-solidity/pull/2
- **Issue:** https://github.com/intellij-solidity/intellij-solidity/issues/458
- **Transitive import issue:** https://github.com/intellij-solidity/intellij-solidity/issues/453
