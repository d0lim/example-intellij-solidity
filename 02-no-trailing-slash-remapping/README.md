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
