# E2E Solidity Test Projects Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Create 6 independent example Solidity projects that cover every E2E Test Plan scenario from [PR #2](https://github.com/d0lim/intellij-solidity/pull/2), enabling manual verification of remapping fixes in IntelliJ.

**Architecture:** Flat numbered directories (`01-xxx/` through `06-xxx/`) under the repo root. Each directory is a self-contained project with config files, minimal `.sol` skeletons, and a README describing the test procedure. No build tooling or dependencies — just static files for the plugin to parse.

**Tech Stack:** Solidity 0.8.0 (skeleton contracts), `remappings.txt`, `foundry.toml`

---

### Task 1: Initialize repo and create root README

**Files:**
- Create: `README.md`

**Step 1: Initialize git repo**

```bash
cd /Users/limdongyoung0/Develop/d0lim/example-intellij-solidity
git init
```

**Step 2: Create root README**

Create `README.md` with the following content:

```markdown
# Example Solidity Projects for intellij-solidity E2E Testing

Example Solidity projects for manually verifying the E2E Test Plan from
[PR #2: fix remappings.txt with arbitrary target paths](https://github.com/d0lim/intellij-solidity/pull/2).

## Projects

| # | Directory | What It Tests |
|---|-----------|---------------|
| 1 | `01-node-modules-remapping/` | Remappings pointing to `node_modules/` targets (issue #458 core) |
| 2 | `02-no-trailing-slash-remapping/` | Remappings without trailing slash |
| 3 | `03-foundry-toml-remapping/` | Remappings defined in `foundry.toml` |
| 4 | `04-custom-target-dir-remapping/` | Custom target directories (soldeer-style) |
| 5 | `05-optimize-imports-transitive/` | Optimize Imports does not add duplicate transitive imports |
| 6 | `06-mixed-remapping-sources/` | Mixed remapping sources (`remappings.txt` + `foundry.toml`) |

## How to Use

1. Build the plugin: `./gradlew runIde` in the [intellij-solidity](https://github.com/d0lim/intellij-solidity) repo (on the PR #2 branch)
2. In the launched IntelliJ instance, open one of the numbered directories as a project
3. Follow the README inside each project for step-by-step test instructions
4. Repeat for each scenario

## Related

- **PR:** https://github.com/d0lim/intellij-solidity/pull/2
- **Issue:** https://github.com/intellij-solidity/intellij-solidity/issues/458
```

**Step 3: Commit**

```bash
git add README.md
git commit -m "docs: add root README with project overview"
```

---

### Task 2: Create scenario 01 — node_modules remapping

**Files:**
- Create: `01-node-modules-remapping/remappings.txt`
- Create: `01-node-modules-remapping/node_modules/@chainlink/contracts/src/Token.sol`
- Create: `01-node-modules-remapping/src/Main.sol`
- Create: `01-node-modules-remapping/README.md`

**Step 1: Create directory structure and config**

Create `01-node-modules-remapping/remappings.txt`:
```
@chainlink=node_modules/@chainlink/contracts
```

**Step 2: Create Solidity files**

Create `01-node-modules-remapping/node_modules/@chainlink/contracts/src/Token.sol`:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Token {}
```

Create `01-node-modules-remapping/src/Main.sol`:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

// Test: type "Token" here and trigger auto-import (Alt+Enter)
// Expected: import "@chainlink/src/Token.sol";

contract Main {}
```

**Step 3: Create README**

Create `01-node-modules-remapping/README.md`:
```markdown
# Scenario 1: Remappings with `node_modules` Target

**Tests:** Remappings pointing to `node_modules/` targets resolve correctly (issue [#458](https://github.com/intellij-solidity/intellij-solidity/issues/458) core scenario).

## File Structure

```
├── remappings.txt                              # @chainlink=node_modules/@chainlink/contracts
├── node_modules/@chainlink/contracts/src/Token.sol
├── src/Main.sol
└── README.md
```

## Test Procedure

1. Open this directory as a project in IntelliJ with the plugin installed (`./gradlew runIde`)
2. Open `src/Main.sol`
3. Type a contract name from `Token.sol` (e.g., `Token`)
4. Trigger auto-import (Alt+Enter or Optimize Imports)
5. **Expected:** Import resolves as `import "@chainlink/src/Token.sol";` — NOT unresolved or broken path
6. Verify no red squiggly underlines on the import statement
7. Ctrl+Click the import path → should navigate to `Token.sol`

## Related

- [PR #2](https://github.com/d0lim/intellij-solidity/pull/2)
- [Issue #458](https://github.com/intellij-solidity/intellij-solidity/issues/458)
```

**Step 4: Verify files exist**

```bash
find 01-node-modules-remapping -type f | sort
```

Expected output:
```
01-node-modules-remapping/README.md
01-node-modules-remapping/node_modules/@chainlink/contracts/src/Token.sol
01-node-modules-remapping/remappings.txt
01-node-modules-remapping/src/Main.sol
```

**Step 5: Commit**

```bash
git add 01-node-modules-remapping/
git commit -m "feat: add scenario 01 — node_modules remapping"
```

---

### Task 3: Create scenario 02 — no trailing slash remapping

**Files:**
- Create: `02-no-trailing-slash-remapping/remappings.txt`
- Create: `02-no-trailing-slash-remapping/lib/openzeppelin/token/ERC20.sol`
- Create: `02-no-trailing-slash-remapping/src/Main.sol`
- Create: `02-no-trailing-slash-remapping/README.md`

**Step 1: Create directory structure and config**

Create `02-no-trailing-slash-remapping/remappings.txt`:
```
@oz=lib/openzeppelin
```

Note: NO trailing slash — this is the key test case.

**Step 2: Create Solidity files**

Create `02-no-trailing-slash-remapping/lib/openzeppelin/token/ERC20.sol`:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract ERC20 {}
```

Create `02-no-trailing-slash-remapping/src/Main.sol`:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

// Test: type "ERC20" here and trigger auto-import (Alt+Enter)
// Expected: import "@oz/token/ERC20.sol";

contract Main {}
```

**Step 3: Create README**

Create `02-no-trailing-slash-remapping/README.md`:
```markdown
# Scenario 2: Remappings Without Trailing Slash

**Tests:** Remappings without a trailing slash still resolve correctly after path normalization.

## File Structure

```
├── remappings.txt                      # @oz=lib/openzeppelin  (no trailing slash!)
├── lib/openzeppelin/token/ERC20.sol
├── src/Main.sol
└── README.md
```

## Test Procedure

1. Open this directory as a project in IntelliJ with the plugin installed
2. Open `src/Main.sol`
3. Type `ERC20` and trigger auto-import (Alt+Enter)
4. **Expected:** Import resolves as `import "@oz/token/ERC20.sol";`
5. Ctrl+Click the import path → should navigate to `ERC20.sol`

## Key Detail

The `remappings.txt` uses `@oz=lib/openzeppelin` **without** a trailing slash. Before the fix, this caused path concatenation errors like `openzeppelintoken/ERC20.sol`.

## Related

- [PR #2](https://github.com/d0lim/intellij-solidity/pull/2)
```

**Step 4: Verify files exist**

```bash
find 02-no-trailing-slash-remapping -type f | sort
```

**Step 5: Commit**

```bash
git add 02-no-trailing-slash-remapping/
git commit -m "feat: add scenario 02 — no trailing slash remapping"
```

---

### Task 4: Create scenario 03 — foundry.toml remapping

**Files:**
- Create: `03-foundry-toml-remapping/foundry.toml`
- Create: `03-foundry-toml-remapping/lib/forge-std/src/Test.sol`
- Create: `03-foundry-toml-remapping/src/Main.sol`
- Create: `03-foundry-toml-remapping/README.md`

**Step 1: Create config**

Create `03-foundry-toml-remapping/foundry.toml`:
```toml
[profile.default]
remappings = ["forge-std/=lib/forge-std/src/"]
```

**Step 2: Create Solidity files**

Create `03-foundry-toml-remapping/lib/forge-std/src/Test.sol`:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Test {}
```

Create `03-foundry-toml-remapping/src/Main.sol`:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

// Test: type "Test" here and trigger auto-import (Alt+Enter)
// Expected: import "forge-std/Test.sol";

contract Main {}
```

**Step 3: Create README**

Create `03-foundry-toml-remapping/README.md`:
```markdown
# Scenario 3: `foundry.toml` Remappings

**Tests:** Remappings defined in `foundry.toml` (instead of `remappings.txt`) resolve correctly.

## File Structure

```
├── foundry.toml                        # [profile.default] remappings = ["forge-std/=lib/forge-std/src/"]
├── lib/forge-std/src/Test.sol
├── src/Main.sol
└── README.md
```

## Test Procedure

1. Open this directory as a project in IntelliJ with the plugin installed
2. Open `src/Main.sol`
3. Type `Test` and trigger auto-import (Alt+Enter)
4. **Expected:** Import resolves as `import "forge-std/Test.sol";`
5. Ctrl+Click the import path → should navigate to `Test.sol`

## Key Detail

This project has NO `remappings.txt` — remappings are defined only in `foundry.toml` under `[profile.default]`.

## Related

- [PR #2](https://github.com/d0lim/intellij-solidity/pull/2)
```

**Step 4: Verify and commit**

```bash
find 03-foundry-toml-remapping -type f | sort
git add 03-foundry-toml-remapping/
git commit -m "feat: add scenario 03 — foundry.toml remapping"
```

---

### Task 5: Create scenario 04 — custom target directory remapping

**Files:**
- Create: `04-custom-target-dir-remapping/remappings.txt`
- Create: `04-custom-target-dir-remapping/dependencies/@deps/token/ERC20.sol`
- Create: `04-custom-target-dir-remapping/src/Main.sol`
- Create: `04-custom-target-dir-remapping/README.md`

**Step 1: Create config**

Create `04-custom-target-dir-remapping/remappings.txt`:
```
@deps/=dependencies/@deps/
```

**Step 2: Create Solidity files**

Create `04-custom-target-dir-remapping/dependencies/@deps/token/ERC20.sol`:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract ERC20 {}
```

Create `04-custom-target-dir-remapping/src/Main.sol`:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

// Test: type "ERC20" here and trigger auto-import (Alt+Enter)
// Expected: import "@deps/token/ERC20.sol";

contract Main {}
```

**Step 3: Create README**

Create `04-custom-target-dir-remapping/README.md`:
```markdown
# Scenario 4: Custom Remapping Target Directories

**Tests:** Remappings to non-standard target directories (e.g., soldeer-style `dependencies/`) resolve correctly.

## File Structure

```
├── remappings.txt                      # @deps/=dependencies/@deps/
├── dependencies/@deps/token/ERC20.sol
├── src/Main.sol
└── README.md
```

## Test Procedure

1. Open this directory as a project in IntelliJ with the plugin installed
2. Open `src/Main.sol`
3. Type `ERC20` and trigger auto-import (Alt+Enter)
4. **Expected:** Import resolves as `import "@deps/token/ERC20.sol";`
5. Ctrl+Click the import path → should navigate to `ERC20.sol`

## Key Detail

Before the fix, `buildImportPath()` reverse remapping only worked with `lib/` targets. This scenario uses `dependencies/` to verify arbitrary target directories now work.

## Related

- [PR #2](https://github.com/d0lim/intellij-solidity/pull/2)
```

**Step 4: Verify and commit**

```bash
find 04-custom-target-dir-remapping -type f | sort
git add 04-custom-target-dir-remapping/
git commit -m "feat: add scenario 04 — custom target dir remapping"
```

---

### Task 6: Create scenario 05 — optimize imports transitive

**Files:**
- Create: `05-optimize-imports-transitive/remappings.txt`
- Create: `05-optimize-imports-transitive/src/A.sol`
- Create: `05-optimize-imports-transitive/src/B.sol`
- Create: `05-optimize-imports-transitive/src/C.sol`
- Create: `05-optimize-imports-transitive/README.md`

**Step 1: Create config**

Create `05-optimize-imports-transitive/remappings.txt` (empty file — no remappings needed):
```
```

**Step 2: Create Solidity files**

Create `05-optimize-imports-transitive/src/C.sol`:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Initializable {}
```

Create `05-optimize-imports-transitive/src/B.sol`:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "./C.sol";

contract B {}
```

Create `05-optimize-imports-transitive/src/A.sol`:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "./B.sol";

contract A is Initializable {}
```

**Step 3: Create README**

Create `05-optimize-imports-transitive/README.md`:
```markdown
# Scenario 5: Optimize Imports — No Duplicate Transitive Imports

**Tests:** Running Optimize Imports does not add redundant transitive import statements.

## File Structure

```
├── remappings.txt                      # (empty)
├── src/A.sol                           # import "./B.sol"; contract A is Initializable {}
├── src/B.sol                           # import "./C.sol"; contract B {}
├── src/C.sol                           # contract Initializable {}
└── README.md
```

## Import Chain

```
A.sol → B.sol → C.sol (defines Initializable)
```

`A.sol` uses `Initializable` which is defined in `C.sol`, but `C.sol` is already reachable transitively via `B.sol`.

## Test Procedure

1. Open this directory as a project in IntelliJ with the plugin installed
2. Open `src/A.sol`
3. Run Optimize Imports (Ctrl+Alt+O / Cmd+Alt+O on macOS)
4. **Expected:** No new `import "./C.sol";` is added — `Initializable` is already reachable via `B.sol → C.sol`
5. Run Optimize Imports again
6. **Expected:** Imports remain stable (no oscillation — imports don't keep changing)

## Key Detail

Before the fix (integrating `preferElementsFromDirectImports()`), the optimizer would add `import "./C.sol";` because it didn't recognize that `Initializable` was already reachable transitively.

## Related

- [PR #2](https://github.com/d0lim/intellij-solidity/pull/2)
- [Issue #453](https://github.com/intellij-solidity/intellij-solidity/issues/453)
```

**Step 4: Verify and commit**

```bash
find 05-optimize-imports-transitive -type f | sort
git add 05-optimize-imports-transitive/
git commit -m "feat: add scenario 05 — optimize imports transitive"
```

---

### Task 7: Create scenario 06 — mixed remapping sources

**Files:**
- Create: `06-mixed-remapping-sources/remappings.txt`
- Create: `06-mixed-remapping-sources/foundry.toml`
- Create: `06-mixed-remapping-sources/lib/utils/Helper.sol`
- Create: `06-mixed-remapping-sources/lib/core/Base.sol`
- Create: `06-mixed-remapping-sources/src/Main.sol`
- Create: `06-mixed-remapping-sources/README.md`

**Step 1: Create config files**

Create `06-mixed-remapping-sources/remappings.txt`:
```
@utils=lib/utils
```

Create `06-mixed-remapping-sources/foundry.toml`:
```toml
[profile.default]
remappings = ["@core/=lib/core/"]
```

**Step 2: Create Solidity files**

Create `06-mixed-remapping-sources/lib/utils/Helper.sol`:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Helper {}
```

Create `06-mixed-remapping-sources/lib/core/Base.sol`:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Base {}
```

Create `06-mixed-remapping-sources/src/Main.sol`:
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

// Test: type "Helper" and trigger auto-import → expected: import "@utils/Helper.sol";
// Test: type "Base" and trigger auto-import → expected: import "@core/Base.sol";

contract Main {}
```

**Step 3: Create README**

Create `06-mixed-remapping-sources/README.md`:
```markdown
# Scenario 6: Mixed Remapping Sources

**Tests:** Remappings from both `remappings.txt` and `foundry.toml` work simultaneously.

## File Structure

```
├── remappings.txt                      # @utils=lib/utils
├── foundry.toml                        # [profile.default] remappings = ["@core/=lib/core/"]
├── lib/utils/Helper.sol
├── lib/core/Base.sol
├── src/Main.sol
└── README.md
```

## Test Procedure

1. Open this directory as a project in IntelliJ with the plugin installed
2. Open `src/Main.sol`
3. Type `Helper` and trigger auto-import (Alt+Enter)
4. **Expected:** Import resolves as `import "@utils/Helper.sol";`
5. Type `Base` and trigger auto-import (Alt+Enter)
6. **Expected:** Import resolves as `import "@core/Base.sol";`
7. Verify both imports have no red squiggly underlines
8. Ctrl+Click each import path → should navigate to the correct file

## Key Detail

`@utils` comes from `remappings.txt`, `@core` comes from `foundry.toml`. Both should resolve without conflict.

## Related

- [PR #2](https://github.com/d0lim/intellij-solidity/pull/2)
```

**Step 4: Verify and commit**

```bash
find 06-mixed-remapping-sources -type f | sort
git add 06-mixed-remapping-sources/
git commit -m "feat: add scenario 06 — mixed remapping sources"
```

---

### Task 8: Final verification

**Step 1: Verify complete project structure**

```bash
find . -type f -not -path './.git/*' -not -path './docs/*' | sort
```

Expected output (23 files):
```
./01-node-modules-remapping/README.md
./01-node-modules-remapping/node_modules/@chainlink/contracts/src/Token.sol
./01-node-modules-remapping/remappings.txt
./01-node-modules-remapping/src/Main.sol
./02-no-trailing-slash-remapping/README.md
./02-no-trailing-slash-remapping/lib/openzeppelin/token/ERC20.sol
./02-no-trailing-slash-remapping/remappings.txt
./02-no-trailing-slash-remapping/src/Main.sol
./03-foundry-toml-remapping/README.md
./03-foundry-toml-remapping/foundry.toml
./03-foundry-toml-remapping/lib/forge-std/src/Test.sol
./03-foundry-toml-remapping/src/Main.sol
./04-custom-target-dir-remapping/README.md
./04-custom-target-dir-remapping/dependencies/@deps/token/ERC20.sol
./04-custom-target-dir-remapping/remappings.txt
./04-custom-target-dir-remapping/src/Main.sol
./05-optimize-imports-transitive/README.md
./05-optimize-imports-transitive/remappings.txt
./05-optimize-imports-transitive/src/A.sol
./05-optimize-imports-transitive/src/B.sol
./05-optimize-imports-transitive/src/C.sol
./06-mixed-remapping-sources/README.md
./06-mixed-remapping-sources/foundry.toml
./06-mixed-remapping-sources/lib/core/Base.sol
./06-mixed-remapping-sources/lib/utils/Helper.sol
./06-mixed-remapping-sources/remappings.txt
./06-mixed-remapping-sources/src/Main.sol
./README.md
```

**Step 2: Verify each remappings.txt/foundry.toml has correct content**

```bash
for f in $(find . -name "remappings.txt" -o -name "foundry.toml" | sort); do echo "=== $f ==="; cat "$f"; echo; done
```

**Step 3: Verify git log**

```bash
git log --oneline
```

Expected: 7 commits (1 root + 6 scenarios).
