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
