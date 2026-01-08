# Feature Specification: Installer MVP (Linux/macOS Tier 1 + Windows doctor-only)

<!--
SOURCES
- mvp/DOD.md
- mvp/BACKLOG_V2.md
-->

---

## 1. OBJECTIVE

### Metadata
- **Category**: Spec
- **Level**: 2
- **Tags**: installer, mvp
- **Priority**: P0
- **Feature Branch**: `feature/install_scripts` (existing)
- **Created**: 2026-01-08
- **Status**: Draft
- **Input**: Build an installer system with doctor/install/update/rollback/uninstall contract, bootstrap without Node/Python, managed-surface policy, Windows doctor-only.

### Stakeholders
- Engineering (repo maintainers)

### Problem Statement
Today, onboarding and maintaining this dev environment requires manual steps and tool-specific install guides. We need a consistent installer interface with predictable outputs, safe rollback, and minimal side effects.

### Purpose
Provide a platform-aware installer interface that can validate prerequisites, manage only a safe surface by default, and support updates and rollback for what it manages.

### Assumptions
- Linux and macOS are Tier 1 for the MVP installer binary release.
- Windows MVP is **doctor-only** via PowerShell, and installation is guided to WSL2.
- The installer **must not** require Node/Python to run `doctor` (bootstrap constraint).

---

## 2. SCOPE

### In Scope
- CLI contract on Linux/macOS: `doctor`, `install`, `update`, `rollback`, `uninstall` with required flags.
- `doctor --json` output schema with stable `output_version: "1"`.
- Managed surface policy: default operations limited to `~/.opencode/`.
- Persistent state and snapshots limited to managed surface.
- Logs under managed surface with `run_id` correlation.
- Windows `.ps1` doctor-only script with explicit "use WSL2" guidance.

### Out of Scope
- Full Windows install/update/rollback/uninstall (explicitly out for MVP).
- Modifying user dotfiles by default (e.g., `~/.zshrc`, `~/.bashrc`).
- Creating project-root `.env` or `.utcp_config.json` by default.
- Advanced packaging automation beyond producing Linux/macOS artifacts for the MVP gate.

---

## 3. USERS & STORIES

### User Story 1 — Doctor (Priority: P0)
As a developer, I want to run `installer doctor` (or `installer.ps1 doctor` on Windows) to deterministically learn whether installation can proceed, and to get machine-readable JSON output for automation.

**Why This Priority**: Doctor is the bootstrap entrypoint and must work without Node/Python (DoD-04).

**Independent Test**:
- Linux/macOS: `./installer doctor` exit 0 and prints PASS/FAIL checks.
- Linux/macOS: `./installer doctor --json` includes `output_version:"1"`.
- Windows: `installer.ps1 doctor` prints a clear doctor-only message.

**Acceptance Scenarios**
1. **Given** Node and Python are not in PATH, **When** `./installer doctor` runs, **Then** it exits 0 and does not fail due to missing Node/Python.
2. **Given** running `./installer doctor --json`, **When** output is parsed, **Then** it contains versioned schema fields (see DoD-08/26).

---

### User Story 2 — Install (Priority: P1)
As a developer, I want to run `installer install` to set up the dev environment in an idempotent way, without touching dotfiles by default.

**Why This Priority**: Install is the core value but constrained to a safe managed surface in MVP.

**Independent Test**:
- Run `install` twice; second run reports no changes.
- `install --dry-run` does not mutate state.

---

### User Story 3 — Update/Rollback/Uninstall (Priority: P1)
As a developer, I want safe updates and ability to rollback/uninstall what the installer manages, without leaving inconsistent state.

**Why This Priority**: Maintains trust; without rollback, updates are risky.

**Independent Test**:
- `update` failure preserves `last_good_snapshot`.
- `rollback --to <id>` restores previous snapshot.
- `uninstall` only removes managed files.

---

## 4. FUNCTIONAL REQUIREMENTS

- **REQ-FUNC-001:** Linux/macOS CLI MUST expose subcommands: `doctor`, `install`, `update`, `rollback`, `uninstall`. (DoD-05)
- **REQ-FUNC-002:** CLI MUST expose flags: `--config`, `--non-interactive`, `--dry-run`, `--json`, `--verbose`. (DoD-06)
- **REQ-FUNC-003:** `--config` MUST fail explicitly when the file does not exist. (DoD-07)
- **REQ-DATA-001:** `doctor --json` MUST include `output_version: "1"` and stable top-level fields. (DoD-08/26)
- **REQ-FUNC-004:** `install --dry-run` MUST NOT mutate persisted state. (DoD-09)
- **REQ-FUNC-005:** Installer MUST only modify within `~/.opencode/` by default and MUST NOT touch dotfiles unless explicitly opted-in. (DoD-10)
- **REQ-FUNC-006:** Windows MVP MUST provide `.ps1` doctor-only that instructs using WSL2 for installation. (DoD-03)

### Traceability Mapping
| User Story | Related Requirements | Notes |
|---|---|---|
| US1 Doctor | REQ-FUNC-001, REQ-DATA-001, REQ-FUNC-006 | Doctor JSON is the contract foundation |
| US2 Install | REQ-FUNC-001, REQ-FUNC-002, REQ-FUNC-004, REQ-FUNC-005 | Idempotent, safe scope |
| US3 Update/Rollback/Uninstall | REQ-DATA-001, REQ-FUNC-005 | Transactional safety tied to state model |

---

## 5. NON-FUNCTIONAL REQUIREMENTS

- **NFR-BOOT-001:** `doctor` MUST run without Node/Python. (DoD-04)
- **NFR-SAFETY-001:** Default operations MUST not modify user dotfiles or project-root dotfiles. (DoD-10)
- **NFR-OPS-001:** Logs MUST be persisted under `~/.opencode/` and correlated with `run_id`. (DoD-29/30)

---

## 6. EDGE CASES

- Missing permissions to create/write under `~/.opencode/`.
- Low disk space for managed surface.
- Partial failures during update/rollback requiring transactional behavior.

---

## 7. SUCCESS CRITERIA

- DoD items in `mvp/DOD.md` are satisfied for MVP scope (Linux/macOS Tier 1 + Windows doctor-only).

---

## 8. DEPENDENCIES & RISKS

### Dependencies
- Packaging approach and language choice for the installer binary (UNKNOWN until implementation decision).

### Risk Assessment
- Choosing a stack that accidentally introduces Node/Python runtime dependency for `doctor`.
- Scope creep into dotfile management.

---

## 9. OUT OF SCOPE (Explicit)

- Full Windows installation flow.
- Non-managed-surface rollback/uninstall.

---

## 10. OPEN QUESTIONS

- **UNKNOWN:** Final implementation language/tooling for the installer binary.
- **UNKNOWN:** Exact structure and location of installer source code inside repo (will be decided in PLAN/DESIGN).

---

## 11. APPENDIX

### References
- `mvp/DOD.md`
- `mvp/BACKLOG_V2.md`

---

## 12. WORKING FILES

Temporary work goes to:
- `mvp/specs/001-installer-mvp/scratch/`

Session context goes to:
- `mvp/specs/001-installer-mvp/memory/`
