# QA Test Plan Template

## Task / Requirement
- Task ID:
- Approved PRD / Acceptance version:
- Approved Art / UI / VFX artifacts (if applicable):
- Tech Design / Client / Server Feature Brief references:

## Scope
### Client / Web
- Functional areas to test:
- UI states to test:
- Visual checkpoints:

### Server
- APIs / messages / business rules to test:
- Persistence / reward / state changes to verify:

## Out of Scope by Default
Unless explicitly requested by the user/task:
- WeChat / Douyin platform testing
- Network / weak-network / reconnect testing
- Performance / memory / load testing
- Broad regression testing outside the changed feature

## Test Cases to Prepare
- `CLIENT_TEST_CASES.md`
- `SERVER_TEST_CASES.md`
- Visual checkpoints/evidence where applicable

## Visual QA Plan
- Fixed Web viewport / resolution:
- Required UI states:
- Reference artifact paths:
- Screenshot checkpoints:
- VFX fixed trigger / camera / key timestamps:
- Manual reviewer: Art / UI / VFX as applicable

## Release Gate
- P0: must be 0
- P1: must be 0
- P2: may carry to next version but must be listed in `TEST_REPORT.md`

## Open Risks / Explicit Exclusions
- 
