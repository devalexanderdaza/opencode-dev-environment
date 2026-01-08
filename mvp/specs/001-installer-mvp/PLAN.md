# Implementation Plan: Installer MVP - Technical Approach & Architecture

<!--
SOURCES
- mvp/DOD.md
- mvp/BACKLOG_V2.md
-->

---

## 1. OBJECTIVE

### Metadata
- **Category**: Plan
- **Tags**: installer, mvp
- **Priority**: P0-critical
- **Branch**: `feature/install_scripts`
- **Date**: 2026-01-08
- **Spec**: `mvp/specs/001-installer-mvp/SPEC.md`

### Summary
Implement an MVP installer interface with a stable CLI contract and versioned outputs, strict managed-surface behavior (`~/.opencode/` by default), and rollback-safe state management. MVP includes Linux/macOS Tier 1 binary deliverables and Windows doctor-only PowerShell.

### Technical Context
- **Language/Version**: UNKNOWN (decision deferred)
- **Storage**: Files under `~/.opencode/` (state, snapshots, logs)
- **Testing**: Minimal / optional (per current preference); emphasize deterministic CLI verification.
- **Target Platform**: Linux + macOS (Tier 1), Windows (doctor-only)
- **Constraints**: `doctor` must run without Node/Python; no dotfile changes by default.

---

## 2. QUALITY GATES

### Definition of Ready (DoR)
- [ ] DoD and Backlog sources confirmed as frozen inputs
- [ ] Managed surface policy explicitly documented (no-dotfiles-by-default)
- [ ] JSON schema version fixed (`output_version: "1"`)

### Definition of Done (DoD)
- [ ] Items DoD-01..DoD-33 in `mvp/DOD.md` satisfied for MVP scope
- [ ] Spec docs exist (SPEC/PLAN/DESIGN/TASK/CHECKLIST)
- [ ] No scope creep beyond `mvp/DOD.md` and `mvp/BACKLOG_V2.md`

### Rollback Guardrails
- All rollback/uninstall operations are limited to the managed surface (default) and must be transactional.

---

## 3. PROJECT STRUCTURE

### Documentation (This Feature)

```
mvp/specs/001-installer-mvp/
  SPEC.md
  PLAN.md
  DESIGN.md
  TASK.md
  CHECKLIST.md
  scratch/
  memory/
```

### Source Code (Repository Root)

**UNKNOWN** until the installer stack choice is made. Candidate options (for later):
- `installer/` or `tools/installer/` for the binary sources
- `scripts/installer/` for bootstrap wrappers

---

## 4. IMPLEMENTATION PHASES

### Phase 0: Discovery (Short)
- Confirm constraints from DoD and existing install guides.
- Decide installer stack (Go/Rust/scripts-only) with a bias to bootstrap without Node/Python.

### Phase 1: Contract & Schema (Blocking)
- Implement CLI contract and help output.
- Implement `doctor --json` stable schema with `output_version: "1"`.

### Phase 2: State + Logs Foundation (Blocking)
- Implement state persistence, snapshot model, and `run_id` correlated logging under `~/.opencode/`.

### Phase 3: Doctor Tier 1 (Parallelizable after Phase 2)
- Linux/macOS doctor checks: writable managed surface + disk space, detect `opencode.json`, etc.
- Windows `.ps1` doctor-only.

### Phase 4: Install/Update/Rollback/Uninstall (Incremental)
- Implement `install` idempotency and safe scope.
- Implement update safety and snapshot creation.
- Implement rollback (transactional) and uninstall (managed-only).

### Phase 5: Packaging + Docs
- Produce Linux/macOS artifacts.
- Document quickstart and Windows WSL2 guidance.

---

## 5. TESTING STRATEGY

- Primary verification is CLI-based acceptance checks from `mvp/DOD.md`.
- Avoid heavy automated test suites; add minimal smoke checks only if necessary.

---

## 6. RISKS & MITIGATIONS

| Risk | Impact | Likelihood | Mitigation |
|---|---|---:|---|
| Runtime depends on Node/Python inadvertently | High | Med | Keep core in a single binary; verify DoD-04 early |
| Dotfile changes creep in | High | Low | Enforce managed-surface policy in design + CLI validation |
| Output schema churn | Med | Med | Fix `output_version`, document schema contract |

---

## 7. DEPENDENCIES

- Packaging decisions for Linux/macOS binaries (T01/T02).
- Windows doctor-only script content (T12/T13).

---

## 8. COMMUNICATION & REVIEW

- Use task parallelization rules in `TASK.md` to avoid conflicting edits.

---

## 9. REFERENCES

- `mvp/DOD.md`
- `mvp/BACKLOG_V2.md`
