# MVP Checklist: Installer MVP - Validation Items (Derived from DoD)

<!--
Primary source: mvp/DOD.md
-->

---

## 1. OBJECTIVE

### Metadata
- **Category**: Checklist
- **Tags**: installer, mvp
- **Priority**: P0-critical
- **Type**: Testing & QA
- **Created**: 2026-01-08
- **Status**: Draft

### Purpose
Operational checklist mapping the MVP Definition of Done (`mvp/DOD.md`) into verifiable items.

---

## 2. LINKS

- **Spec**: `mvp/specs/001-installer-mvp/SPEC.md`
- **Plan**: `mvp/specs/001-installer-mvp/PLAN.md`
- **Design**: `mvp/specs/001-installer-mvp/DESIGN.md`
- **Tasks**: `mvp/specs/001-installer-mvp/TASK.md`
- **DoD Source**: `mvp/DOD.md`

---

## 3. CHECKLIST (DoD-01..DoD-33)

### Artifacts
- [ ] DoD-01 Linux Tier 1: binario entregable
- [ ] DoD-02 macOS Tier 1: binario entregable
- [ ] DoD-03 Windows MVP: `.ps1` doctor-only
- [ ] DoD-04 Bootstrap: doctor sin Node/Python

### CLI Contract
- [ ] DoD-05 Subcomandos: doctor/install/update/rollback/uninstall
- [ ] DoD-06 Flags mínimos: --config/--non-interactive/--dry-run/--json/--verbose
- [ ] DoD-07 --config inexistente falla explícito
- [ ] DoD-08 doctor --json incluye output_version "1"
- [ ] DoD-09 dry-run no muta estado

### Scope & Surface
- [ ] DoD-10 Managed surface `~/.opencode/` + no dotfiles por defecto
- [ ] DoD-11 Reporta scope (project/global)

### Doctor
- [ ] DoD-12 Doctor Tier 1 Linux/macOS (PASS/FAIL)
- [ ] DoD-13 Detecta opencode.json

### Install
- [ ] DoD-14 Install idempotente
- [ ] DoD-15 Install soporta alcance (core vs all)
- [ ] DoD-16 Install registra cambios (managed surface)
- [ ] DoD-17 Install post-install verify

### Update
- [ ] DoD-18 Update existe y documenta qué actualiza
- [ ] DoD-19 Update seguro ante fallos
- [ ] DoD-20 Update crea snapshot nuevo

### Rollback
- [ ] DoD-21 Rollback requiere target explícito
- [ ] DoD-22 Rollback restaura snapshot
- [ ] DoD-23 Rollback transaccional (managed surface)

### Uninstall
- [ ] DoD-24 Uninstall seguro (solo lo gestionado)
- [ ] DoD-25 Uninstall confirmación o --non-interactive

### State / Snapshots
- [ ] DoD-26 Estado persistente mínimo
- [ ] DoD-27 Manifiesto de archivos gestionados con hash
- [ ] DoD-28 Historial de snapshots consultable

### Logs
- [ ] DoD-29 Logs persistentes bajo `~/.opencode/` + ruta mostrada
- [ ] DoD-30 Logs correlacionados por run_id

### Docs / Process
- [ ] DoD-31 Documentación mínima del instalador
- [ ] DoD-32 Git flow aplicado
- [ ] DoD-33 Conventional commits aplicados

---

## 4. VERIFICATION PROTOCOL

When marking items complete, include evidence, e.g.:

```markdown
- [x] DoD-08 | Evidence: `./installer doctor --json` contains `"output_version":"1"`
```
