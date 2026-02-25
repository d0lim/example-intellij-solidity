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
