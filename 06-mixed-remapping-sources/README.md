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
