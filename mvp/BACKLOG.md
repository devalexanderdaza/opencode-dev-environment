# MVP Backlog (HISTÓRICO)

Este archivo queda como referencia histórica del backlog original.

- Fuente activa: `mvp/BACKLOG_V2.md` (ordenado por streams y alineado con `mvp/DOD.md` usando IDs `DoD-01..DoD-33`).
- Motivo: el DoD fue consolidado/renumerado para cumplir ≤35 ítems y eliminar duplicados; mantener dos backlogs “activos” genera referencias rotas y contradicciones.
- Importante: las referencias `DoD-xx` dentro de este archivo corresponden a una numeración previa y no deben usarse como trazabilidad actual.
## Notas
**Backlog MVP (1:1 con el DoD)**  
Leyenda: **[BLOQUEANTE]** = bloquea otras tareas; **[PARALELIZABLE]** = se puede ejecutar en paralelo.

**Tareas**
- **T01 [BLOQUEANTE]** Objetivo: definir el “artefacto Linux” (formato, nombre, distribución). Impacto: UNKNOWN (nuevo empaquetado CI/release). Dep: T04. Aceptación: cumple DoD-01 (binario Linux `--version` OK).
- **T02 [BLOQUEANTE]** Objetivo: definir el “artefacto macOS” (formato, nombre, distribución). Impacto: UNKNOWN (nuevo empaquetado CI/release). Dep: T04. Aceptación: cumple DoD-02 (binario macOS `--version` OK).
- **T03 [BLOQUEANTE]** Objetivo: garantizar bootstrap sin Node/Python al arrancar (política + verificación en entorno limpio). Impacto: UNKNOWN (nuevo instalador + CI). Dep: T04. Aceptación: cumple DoD-03 (doctor corre sin `node/python3`).
- **T04 [BLOQUEANTE]** Objetivo: definir la CLI mínima y contrato de subcomandos. Impacto: UNKNOWN (nuevo binario/CLI). Dep: —. Aceptación: cumple DoD-04 (`--help` lista `doctor/install/update/rollback/uninstall` si aplica).
- **T05 [BLOQUEANTE]** Objetivo: definir contrato de flags mínimos (`--config`, `--non-interactive`, `--dry-run`, `--json`, `--verbose`). Impacto: UNKNOWN (CLI parser). Dep: T04. Aceptación: cumple DoD-05 (aparecen en `--help`).
- **T06 [PARALELIZABLE]** Objetivo: implementar manejo de `--config` inexistente (error explícito). Impacto: UNKNOWN (carga config). Dep: T05. Aceptación: cumple DoD-06 (exit≠0 + “config not found”).
- **T07 [PARALELIZABLE]** Objetivo: definir salida `--json` estable para `doctor`. Impacto: UNKNOWN (output JSON). Dep: T04, T05. Aceptación: cumple DoD-07 (JSON válido + exit 0).
- **T08 [PARALELIZABLE]** Objetivo: definir semántica y garantías de `--dry-run` (sin cambios de estado). Impacto: UNKNOWN (planificador + estado). Dep: T05, T27. Aceptación: cumple DoD-08 (dry-run no altera estado).
- **T09 [PARALELIZABLE]** Objetivo: doctor Tier 1 Linux/macOS: checks mínimos (disco/permiso/escritura destino) y reporte PASS/FAIL. Impacto: UNKNOWN (doctor checks). Dep: T04. Aceptación: cumple DoD-09.
- **T10 [PARALELIZABLE]** Objetivo: doctor reporta ausencia de Node/npm/Python como “opcional/no bloqueante” (solo informar). Impacto: UNKNOWN (doctor checks). Dep: T04, T07. Aceptación: cumple DoD-10.
- **T11 [PARALELIZABLE]** Objetivo: doctor detecta config existente del repo (p.ej. presencia de opencode.json). Impacto: UNKNOWN (doctor checks). Dep: T04, T07. Aceptación: cumple DoD-11.
- **T12 [PARALELIZABLE]** Objetivo: comportamiento Windows MVP: `doctor` funciona y explicita “doctor-only; usar WSL2 para instalación”. Impacto: UNKNOWN (detección plataforma). Dep: T04. Aceptación: cumple DoD-12.
- **T13 [PARALELIZABLE]** Objetivo: doctor en Windows valida ruta WSL2 (detecta y guía). Impacto: UNKNOWN (doctor checks). Dep: T12. Aceptación: cumple DoD-13.
- **T14 [BLOQUEANTE]** Objetivo: `install` Tier 1 idempotente (segunda ejecución = “no changes”). Impacto: UNKNOWN (motor install + estado). Dep: T04, T27, T28. Aceptación: cumple DoD-14.
- **T15 [PARALELIZABLE]** Objetivo: `install` soporta alcance mínimo vs completo (al menos `core` vs `all`). Impacto: UNKNOWN (config + selección de plan). Dep: T14. Aceptación: cumple DoD-15.
- **T16 [PARALELIZABLE]** Objetivo: `install` en Windows nativo bloquea con mensaje claro (MVP). Impacto: UNKNOWN (detección plataforma). Dep: T04. Aceptación: cumple DoD-16.
- **T17 [PARALELIZABLE]** Objetivo: `install --verbose` lista cambios (create/modify/delete) con paths. Impacto: UNKNOWN (reporting). Dep: T14. Aceptación: cumple DoD-17.
- **T18 [PARALELIZABLE]** Objetivo: `install` ejecuta verificación post-install y resume “VERIFY: PASS/FAIL”. Impacto: UNKNOWN (verifier). Dep: T14, T09–T11. Aceptación: cumple DoD-18.
- **T19 [PARALELIZABLE]** Objetivo: `update --help` describe qué actualiza y fuente (channel/path). Impacto: UNKNOWN (CLI update). Dep: T04, T05. Aceptación: cumple DoD-19.
- **T20 [BLOQUEANTE]** Objetivo: update seguro (si falla, no deja estado inconsistente; conserva “last_good”). Impacto: UNKNOWN (estado transaccional). Dep: T27, T29. Aceptación: cumple DoD-20.
- **T21 [PARALELIZABLE]** Objetivo: update crea snapshot nuevo. Impacto: UNKNOWN (snapshots). Dep: T29, T20. Aceptación: cumple DoD-21.
- **T22 [PARALELIZABLE]** Objetivo: `rollback` exige target explícito (`--to`) y falla si falta. Impacto: UNKNOWN (CLI rollback). Dep: T04, T05. Aceptación: cumple DoD-22.
- **T23 [BLOQUEANTE]** Objetivo: rollback restaura config/archivos gestionados a un snapshot (consistencia hash). Impacto: UNKNOWN (restauración). Dep: T22, T28, T29. Aceptación: cumple DoD-23.
- **T24 [BLOQUEANTE]** Objetivo: rollback transaccional (fallo ⇒ vuelve a pre-rollback). Impacto: UNKNOWN (journaling/transaction). Dep: T23, T27. Aceptación: cumple DoD-24.
- **T25 [PARALELIZABLE]** Objetivo: `uninstall` elimina solo lo gestionado (según manifiesto) y reporta “skipped” lo demás. Impacto: UNKNOWN (uninstall). Dep: T28. Aceptación: cumple DoD-25.
- **T26 [PARALELIZABLE]** Objetivo: `uninstall` requiere confirmación o `--non-interactive`. Impacto: UNKNOWN (prompts/flags). Dep: T05, T25. Aceptación: cumple DoD-26.
- **T27 [BLOQUEANTE]** Objetivo: diseñar/manterner manifiesto de estado persistente (versión, plataforma, timestamp, snapshots). Impacto: UNKNOWN (estado persistente). Dep: T04. Aceptación: cumple DoD-27 (sale por `doctor --json`).
- **T28 [BLOQUEANTE]** Objetivo: manifiesto de archivos gestionados con hash (para rollback/uninstall). Impacto: UNKNOWN (hashing + tracking). Dep: T27. Aceptación: cumple DoD-28.
- **T29 [BLOQUEANTE]** Objetivo: historial de snapshots consultable (por `doctor --json` o `rollback --list`). Impacto: UNKNOWN (snapshots API). Dep: T27. Aceptación: cumple DoD-29.
- **T30 [PARALELIZABLE]** Objetivo: logs persistentes + rotación (ubicación definida y mostrada). Impacto: UNKNOWN (logging). Dep: T04. Aceptación: cumple DoD-30.
- **T31 [PARALELIZABLE]** Objetivo: correlación por ejecución (`run_id` en salida y en log). Impacto: UNKNOWN (logging + doctor output). Dep: T30, T07. Aceptación: cumple DoD-31.
- **T32 [PARALELIZABLE]** Objetivo: documentación mínima del instalador (Quickstart + comandos/flags + ejemplos). Impacto: README.md (o doc nueva: UNKNOWN). Dep: T04, T05. Aceptación: cumple DoD-32.
- **T33 [PARALELIZABLE]** Objetivo: doc explicita Windows MVP = doctor-only + WSL2 como ruta soportada (con base en guías existentes). Impacto: README.md y/o doc nueva: UNKNOWN. Dep: T12–T16. Aceptación: cumple DoD-33.
- **T34 [PARALELIZABLE]** Objetivo: acordar y aplicar Git flow (naming `feature/` `fix/`, PR). Impacto: proceso (no archivos), doc opcional: CONTRIBUTING.md. Dep: —. Aceptación: ver sección de proceso en `mvp/DOD.md`.
- **T35 [PARALELIZABLE]** Objetivo: acordar y aplicar Conventional Commits en el trabajo del instalador. Impacto: proceso (no archivos), doc opcional: CONTRIBUTING.md. Dep: —. Aceptación: ver sección de proceso en `mvp/DOD.md`.

Si quieres, puedo reordenar este backlog en “streams” de trabajo (CLI/Estado/Logs/Docs/Packaging) manteniendo el mapeo 1:1 con el DoD.