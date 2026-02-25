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
