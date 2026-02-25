# intellij-solidity E2E 테스트용 예제 Solidity 프로젝트

> [English](./README.md)

[PR #2: fix remappings.txt with arbitrary target paths](https://github.com/d0lim/intellij-solidity/pull/2)의 E2E Test Plan을 수동으로 검증하기 위한 예제 Solidity 프로젝트 모음입니다.

## 배경

[intellij-solidity](https://github.com/intellij-solidity/intellij-solidity) 플러그인에서 `remappings.txt` 엔트리가 `node_modules/`, 커스텀 디렉토리 등 비표준 타겟을 가리킬 때 resolve에 실패하는 문제가 있었습니다 ([#458](https://github.com/intellij-solidity/intellij-solidity/issues/458)).

PR #2에서 수정한 핵심 사항:
- **trailing slash 정규화** — `parseRemappingsFile()`에서 경로 결합 시 `/` 누락 방지
- **`preferElementsFromDirectImports()` 통합** — 중복 transitive import 추가 방지 ([#453](https://github.com/intellij-solidity/intellij-solidity/issues/453))
- **`buildImportPath()` reverse remapping 확장** — `lib/` 외의 커스텀 타겟 디렉토리 지원
- **`buildReverseRemappings()` trailing-slash 일관성** — prefix 값 정규화

## 프로젝트 목록

| # | 디렉토리 | 테스트 대상 |
|---|----------|------------|
| 1 | [`01-node-modules-remapping/`](./01-node-modules-remapping/) | `node_modules/` 타겟 리매핑 (이슈 #458 핵심) |
| 2 | [`02-no-trailing-slash-remapping/`](./02-no-trailing-slash-remapping/) | trailing slash 없는 리매핑 경로 정규화 |
| 3 | [`03-foundry-toml-remapping/`](./03-foundry-toml-remapping/) | `foundry.toml`에 정의된 리매핑 |
| 4 | [`04-custom-target-dir-remapping/`](./04-custom-target-dir-remapping/) | 커스텀 타겟 디렉토리 리매핑 (soldeer 스타일) |
| 5 | [`05-optimize-imports-transitive/`](./05-optimize-imports-transitive/) | Optimize Imports 시 중복 transitive import 미추가 |
| 6 | [`06-mixed-remapping-sources/`](./06-mixed-remapping-sources/) | `remappings.txt` + `foundry.toml` 혼합 리매핑 |

## 사용 방법

### 사전 준비

- [intellij-solidity](https://github.com/d0lim/intellij-solidity) 레포를 클론하고 PR #2 브랜치로 체크아웃
- JDK 17+ 설치

### 실행 순서

1. 플러그인을 빌드하고 테스트 IDE를 실행합니다:
   ```bash
   cd intellij-solidity
   git checkout fix/remappings-arbitrary-target-paths  # PR #2 브랜치
   ./gradlew runIde
   ```
2. 실행된 IntelliJ 인스턴스에서 번호별 디렉토리를 **프로젝트로 열기** (Open as Project)
3. 각 프로젝트 안의 README에 따라 테스트 수행
4. 모든 시나리오에 대해 반복

## E2E 테스트 체크리스트

### 시나리오 1: `node_modules` 타겟 리매핑

> `remappings.txt`: `@chainlink=node_modules/@chainlink/contracts`

- [ ] `src/Main.sol`에서 `Token` 타이핑 후 auto-import (Alt+Enter)
- [ ] import가 `import "@chainlink/src/Token.sol";`로 resolve됨
- [ ] import 경로에 빨간 밑줄 없음
- [ ] Ctrl+Click으로 `Token.sol`로 네비게이션됨

### 시나리오 2: trailing slash 없는 리매핑

> `remappings.txt`: `@oz=lib/openzeppelin` (trailing slash 없음!)

- [ ] `src/Main.sol`에서 `ERC20` 타이핑 후 auto-import
- [ ] import가 `import "@oz/token/ERC20.sol";`로 resolve됨 (`openzeppelintoken/ERC20.sol` 아님)
- [ ] Ctrl+Click으로 `ERC20.sol`로 네비게이션됨

### 시나리오 3: `foundry.toml` 리매핑

> `foundry.toml`: `remappings = ["forge-std/=lib/forge-std/src/"]` (remappings.txt 없음)

- [ ] `src/Main.sol`에서 `Test` 타이핑 후 auto-import
- [ ] import가 `import "forge-std/Test.sol";`로 resolve됨
- [ ] Ctrl+Click으로 `Test.sol`로 네비게이션됨

### 시나리오 4: 커스텀 타겟 디렉토리 리매핑

> `remappings.txt`: `@deps/=dependencies/@deps/`

- [ ] `src/Main.sol`에서 `ERC20` 타이핑 후 auto-import
- [ ] import가 `import "@deps/token/ERC20.sol";`로 resolve됨
- [ ] Ctrl+Click으로 `ERC20.sol`로 네비게이션됨

### 시나리오 5: Optimize Imports — 중복 transitive import 방지

> `Initializable`가 두 경로에 존재: `contracts-upgradeable/.../Initializable.sol` (직접 import) + `upgrades-core/.../Initializable.sol` (transitive)

- [ ] `src/MyCoin.sol` 열고 Optimize Imports 실행 (Ctrl+Alt+O / Cmd+Alt+O)
- [ ] import가 정확히 2개 유지됨 (OwnableUpgradeable + contracts-upgradeable의 Initializable)
- [ ] `upgrades-core/contracts/Initializable.sol`이 중복 import로 **추가되지 않음**
- [ ] Optimize Imports 재실행 시 import가 변하지 않음 (oscillation 없음)

### 시나리오 6: 혼합 리매핑 소스

> `remappings.txt`: `@utils=lib/utils`
> `foundry.toml`: `remappings = ["@core/=lib/core/"]`

- [ ] `src/Main.sol`에서 `Helper` auto-import → `import "@utils/Helper.sol";`
- [ ] `src/Main.sol`에서 `Base` auto-import → `import "@core/Base.sol";`
- [ ] 두 import 모두 빨간 밑줄 없음
- [ ] 각 import Ctrl+Click으로 올바른 파일로 네비게이션됨

## 관련 링크

- **PR:** https://github.com/d0lim/intellij-solidity/pull/2
- **Issue:** https://github.com/intellij-solidity/intellij-solidity/issues/458
- **Transitive import issue:** https://github.com/intellij-solidity/intellij-solidity/issues/453
