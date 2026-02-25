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
