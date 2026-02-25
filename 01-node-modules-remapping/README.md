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
