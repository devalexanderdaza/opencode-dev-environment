## Backlog MVP reordenado en streams (manteniendo Txx y mapeo 1:1 con DoD)

### 1) Stream: **Contrato CLI/Output**
- **T04 [BLOQUEANTE]** Objetivo: definir la CLI mínima y contrato de subcomandos. Impacto: UNKNOWN (nuevo binario/CLI). Dep: —. Aceptación: DoD-05.
- **T05 [BLOQUEANTE]** Objetivo: definir contrato de flags mínimos (`--config`, `--non-interactive`, `--dry-run`, `--json`, `--verbose`). Impacto: UNKNOWN. Dep: T04. Aceptación: DoD-06.
- **T07 [BLOQUEANTE]** Objetivo: definir salida `doctor --json` estable (incl. `output_version`). Impacto: UNKNOWN. Dep: T04, T05. Aceptación: DoD-08.
- **T06 [PARALELIZABLE]** Objetivo: manejo de `--config` inexistente (error explícito). Impacto: UNKNOWN. Dep: T05. Aceptación: DoD-07.

### 2) Stream: **Estado / Snapshots**
- **T27 [BLOQUEANTE]** Objetivo: manifiesto de estado persistente (versión/plataforma/timestamps/snapshots) bajo `~/.opencode/`. Impacto: UNKNOWN. Dep: T04, T07. Aceptación: DoD-26 + DoD-10.
- **T28 [BLOQUEANTE]** Objetivo: manifiesto de archivos gestionados con hash (para rollback/uninstall) limitado al managed surface. Impacto: UNKNOWN. Dep: T27. Aceptación: DoD-27.
- **T29 [BLOQUEANTE]** Objetivo: historial de snapshots consultable (`rollback --list` o vía `doctor --json`). Impacto: UNKNOWN. Dep: T27. Aceptación: DoD-28.

### 3) Stream: **Doctor**
- **T09 [PARALELIZABLE]** Objetivo: doctor Tier 1 Linux/macOS (checks mínimos + PASS/FAIL). Impacto: UNKNOWN. Dep: T04, T27. Aceptación: DoD-12.
- **T10 [PARALELIZABLE]** Objetivo: doctor reporta ausencia de Node/npm/Python como no-bloqueante. Impacto: UNKNOWN. Dep: T09, T07. Aceptación: DoD-04.
- **T11 [PARALELIZABLE]** Objetivo: doctor detecta `opencode.json` si existe. Impacto: UNKNOWN. Dep: T09. Aceptación: DoD-13.
- **T12 [PARALELIZABLE]** Objetivo: Windows MVP doctor-only (PowerShell `.ps1`, no `.exe`). Impacto: UNKNOWN. Dep: T07. Aceptación: DoD-03.
- **T13 [PARALELIZABLE]** Objetivo: doctor Windows guía/valida ruta WSL2 para instalación (doctor-only). Impacto: UNKNOWN. Dep: T12. Aceptación: DoD-03.

### 4) Stream: **Install**
- **T14 [BLOQUEANTE]** Objetivo: `install` Tier 1 idempotente. Impacto: UNKNOWN. Dep: T27–T29, T05. Aceptación: DoD-14.
- **T15 [PARALELIZABLE]** Objetivo: `install` soporta alcance mínimo vs completo (`core` vs `all`). Impacto: UNKNOWN. Dep: T14. Aceptación: DoD-15.
- **T16 [PARALELIZABLE]** Objetivo: `install --verbose` lista cambios (limitado al managed surface por defecto). Impacto: UNKNOWN. Dep: T14. Aceptación: DoD-16.
- **T17 [PARALELIZABLE]** Objetivo: `install` post-verify y resumen `VERIFY: PASS`. Impacto: UNKNOWN. Dep: T14, T09–T11. Aceptación: DoD-17.
- **T18 [BLOQUEANTE]** Objetivo: enforced managed surface/no dotfiles (default) y dotfiles solo con flag explícito. Impacto: UNKNOWN (instalación + cambios + docs/help). Dep: T05, T27. Aceptación: DoD-10.

### 5) Stream: **Update / Rollback / Uninstall**
- **T19 [PARALELIZABLE]** Objetivo: `update --help` describe qué actualiza y fuente. Impacto: UNKNOWN. Dep: T04, T05. Aceptación: DoD-18.
- **T20 [BLOQUEANTE]** Objetivo: update seguro ante fallos (mantiene `last_good_snapshot`). Impacto: UNKNOWN. Dep: T27–T29, T19. Aceptación: DoD-19.
- **T21 [PARALELIZABLE]** Objetivo: update crea snapshot nuevo en éxito. Impacto: UNKNOWN. Dep: T20. Aceptación: DoD-20.
- **T22 [PARALELIZABLE]** Objetivo: `rollback` exige `--to` y falla si falta. Impacto: UNKNOWN. Dep: T04, T05. Aceptación: DoD-21.
- **T23 [BLOQUEANTE]** Objetivo: rollback restaura a snapshot dentro de `~/.opencode/`. Impacto: UNKNOWN. Dep: T22, T28–T29. Aceptación: DoD-22.
- **T24 [BLOQUEANTE]** Objetivo: rollback transaccional limitado al managed surface. Impacto: UNKNOWN. Dep: T23, T18. Aceptación: DoD-23.
- **T25 [PARALELIZABLE]** Objetivo: `uninstall` elimina solo lo gestionado (manifiesto). Impacto: UNKNOWN. Dep: T28, T18. Aceptación: DoD-24.
- **T26 [PARALELIZABLE]** Objetivo: `uninstall` confirmación o `--non-interactive`. Impacto: UNKNOWN. Dep: T25, T05. Aceptación: DoD-25.

### 6) Stream: **Logs**
- **T30 [PARALELIZABLE]** Objetivo: logs persistentes + rotación bajo `~/.opencode/` y ruta mostrada. Impacto: UNKNOWN. Dep: T27, T04. Aceptación: DoD-29.
- **T31 [PARALELIZABLE]** Objetivo: `run_id` correlaciona salida y log. Impacto: UNKNOWN. Dep: T30, T07. Aceptación: DoD-30.

### 7) Stream: **Packaging / Artifacts**
- **T01 [BLOQUEANTE]** Objetivo: artefacto Linux (formato + nombre + distribución). Impacto: UNKNOWN. Dep: T04 (naming/versioning claro). Aceptación: DoD-01.
- **T02 [BLOQUEANTE]** Objetivo: artefacto macOS (formato + nombre + distribución). Impacto: UNKNOWN. Dep: T04. Aceptación: DoD-02.
- **T03 [BLOQUEANTE]** Objetivo: gate de release: bootstrap sin Node/Python (doctor). Impacto: UNKNOWN. Dep: T01–T02, T04. Aceptación: DoD-04.

### 8) Stream: **Docs / Proceso**
- **T32 [PARALELIZABLE]** Objetivo: documentación mínima (Quickstart + comandos/flags + ejemplos + managed surface/non-goals). Impacto: README.md o doc nueva: UNKNOWN. Dep: T04–T05, T18. Aceptación: DoD-31.
- **T33 [PARALELIZABLE]** Objetivo: docs declaran Windows MVP doctor-only + WSL2 y no-dotfiles + managed surface. Impacto: doc(s) del instalador + referencias a convención global `~/.opencode/` de skills. Dep: T12–T13, T18. Aceptación: DoD-31.
- **T34 [PARALELIZABLE]** Objetivo: Git flow aplicado (feature/fix + PR). Impacto: proceso (doc opcional: CONTRIBUTING.md). Dep: —. Aceptación: DoD-32.
- **T35 [PARALELIZABLE]** Objetivo: Conventional commits aplicados. Impacto: proceso (doc opcional: CONTRIBUTING.md). Dep: —. Aceptación: DoD-33.

Notas:
- Los IDs DoD referenciados aquí corresponden a mvp/DOD.md (DoD-01..DoD-33).