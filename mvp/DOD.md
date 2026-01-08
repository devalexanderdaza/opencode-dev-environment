Ajustes solicitados (con evidencia del repo):
- Ruta global `~/.opencode/...` existe como convención para skills: .opencode/install_guides/README.md.
- Windows soportado como WSL (y hay fricción fuera de WSL): .opencode/install_guides/README.md y .opencode/install_guides/install_scripts/install-chrome-devtools.sh.
- PowerShell ya se usa en Windows para flows “one-click” (referencia): .opencode/install_guides/MCP - Narsil.md y .opencode/install_guides/install_scripts/install-narsil.sh.
- Dotfiles que el ecosistema actual propone en raíz del proyecto (que el MVP debe evitar por defecto): .utcp_config.json y .env aparecen como “Create … in project root” en .opencode/install_guides/README.md y .opencode/install_guides/MCP - Code Mode.md.

## DoD (MVP) — Instalador (Checklist final, ≤35)

- [ ] **DoD-01 Linux Tier 1: binario entregable**. Cómo verificar: en Linux ejecutar `./installer --version` ⇒ exit 0 y muestra versión semver.
- [ ] **DoD-02 macOS Tier 1: binario entregable**. Cómo verificar: en macOS ejecutar `./installer --version` ⇒ exit 0 y muestra versión semver.
- [ ] **DoD-03 Windows MVP = PowerShell `.ps1` doctor-only (sin `.exe`)**. Cómo verificar: en Windows ejecutar `powershell -NoProfile -ExecutionPolicy Bypass -Command "& .\\installer.ps1 doctor | Select-String 'Windows: doctor-only; use WSL2' | Out-Null; if($LASTEXITCODE -ne 0){exit 1}; & .\\installer.ps1 install; if($LASTEXITCODE -eq 0){exit 1} else {exit 0}"` ⇒ exit 0 (doctor OK; install falla con mensaje claro).
- [ ] **DoD-04 Bootstrap: doctor arranca sin Node/Python** (Linux/macOS). Cómo verificar: con `node` y `python3` ausentes de PATH, ejecutar `./installer doctor` ⇒ exit 0 y no falla por “node/python3 not found”.

- [ ] **DoD-05 Contrato CLI (Linux/macOS)**: subcomandos `doctor`, `install`, `update`, `rollback`, `uninstall`. Cómo verificar: ejecutar `./installer --help` ⇒ aparecen esos subcomandos.
- [ ] **DoD-06 Flags mínimos**: `--config`, `--non-interactive`, `--dry-run`, `--json`, `--verbose`. Cómo verificar: ejecutar `./installer --help` ⇒ aparecen los flags con descripción.
- [ ] **DoD-07 Config mínima**: `--config` inexistente falla explícito. Cómo verificar: ejecutar `./installer doctor --config /ruta/inexistente` ⇒ exit ≠0 y mensaje “config not found” (o equivalente).
- [ ] **DoD-08 Esquema JSON versionado**: `doctor --json` incluye `output_version` fijo. Cómo verificar: ejecutar `./installer doctor --json | grep -q '"output_version":"1"'` ⇒ exit 0.
- [ ] **DoD-09 Dry-run no muta estado**. Cómo verificar: ejecutar `./installer install --dry-run` y luego `./installer doctor --json` ⇒ `state_revision` (o equivalente) no cambia vs antes del dry-run.

- [ ] **DoD-10 Managed surface + non-goals (no dotfiles por defecto)**: el instalador solo modifica dentro de `~/.opencode/` en MVP; por defecto no crea `.env`/`.utcp_config.json` en el proyecto ni modifica `~/.zshrc`/`~/.bashrc` sin flag explícito. Cómo verificar: `./installer --help | grep -Ei "(~/.opencode|managed|non-goals|dotfiles)" && H(){ command -v sha256sum >/dev/null && sha256sum "$1" | awk '{print $1}' || shasum -a 256 "$1" | awk '{print $1}'; }; b="$(for f in ~/.zshrc ~/.bashrc; do H "$f" 2>/dev/null; done)"; rm -f ./.env ./.utcp_config.json 2>/dev/null; ./installer install; a="$(for f in ~/.zshrc ~/.bashrc; do H "$f" 2>/dev/null; done)"; test "$b" = "$a" && test ! -e ./.env && test ! -e ./.utcp_config.json` ⇒ exit 0.
- [ ] **DoD-11 Scope reportado**: el instalador reporta `scope` (`project` o `global`). Cómo verificar: ejecutar `./installer doctor --json | grep -Eq '"scope":"(project|global)"'` ⇒ exit 0.

- [ ] **DoD-12 Doctor Tier 1 (Linux/macOS)**: checks mínimos (escritura en managed surface + espacio). Cómo verificar: ejecutar `./installer doctor` ⇒ lista checks con “PASS/FAIL” y exit 0 solo si todo PASS.
- [ ] **DoD-13 Doctor detecta `opencode.json` si existe**. Cómo verificar: en un repo con `opencode.json`, ejecutar `./installer doctor` ⇒ indica “found opencode.json” (o equivalente) y exit 0.

- [ ] **DoD-14 Install Tier 1 idempotente**. Cómo verificar: ejecutar `./installer install` dos veces ⇒ ambas exit 0; la segunda reporta “no changes” (o equivalente).
- [ ] **DoD-15 Install soporta alcance mínimo vs completo** (p. ej. `core` vs `all`). Cómo verificar: ejecutar `./installer install --help` ⇒ muestra selector de alcance y su efecto.
- [ ] **DoD-16 Install registra cambios (limitado al managed surface por defecto)**. Cómo verificar: ejecutar `./installer install --verbose` ⇒ cada ruta reportada empieza con `~/.opencode/` y no aparecen dotfiles del HOME (p. ej. `~/.zshrc`, `~/.bashrc`) salvo flag explícito.
- [ ] **DoD-17 Install valida al final (“post-install verify”)**. Cómo verificar: ejecutar `./installer install` ⇒ termina con resumen “VERIFY: PASS” (o equivalente) y exit 0.

- [ ] **DoD-18 Update existe y documenta qué actualiza y desde dónde**. Cómo verificar: ejecutar `./installer update --help` ⇒ describe fuente (canal/archivo) y objetivo (componentes/estado).
- [ ] **DoD-19 Update es seguro ante fallos** (no deja estado inconsistente). Cómo verificar: forzar fallo (p. ej. fuente inválida) y ejecutar `./installer update` ⇒ exit ≠0 y `./installer doctor --json` mantiene `last_good_snapshot` sin cambios parciales.
- [ ] **DoD-20 Update crea snapshot nuevo en éxito**. Cómo verificar: ejecutar `./installer update` ⇒ `./installer doctor --json` muestra un snapshot adicional en `snapshots[]`.

- [ ] **DoD-21 Rollback requiere target explícito**. Cómo verificar: ejecutar `./installer rollback` sin args ⇒ exit ≠0 y mensaje “must specify --to …” (o equivalente).
- [ ] **DoD-22 Rollback restaura a snapshot** (hash/manifest coherente). Cómo verificar: ejecutar `./installer rollback --to <id>` ⇒ exit 0 y `./installer doctor --json` refleja `current_snapshot=<id>`.
- [ ] **DoD-23 Rollback transaccional (solo managed surface)**. Cómo verificar: provocar error de permisos dentro de `~/.opencode/` durante rollback ⇒ exit ≠0 y `current_snapshot` no cambia vs antes del intento.

- [ ] **DoD-24 Uninstall es seguro (solo elimina lo gestionado)**. Cómo verificar: ejecutar `./installer uninstall` ⇒ exit 0 y reporta “skipped” para archivos fuera del manifiesto.
- [ ] **DoD-25 Uninstall requiere confirmación o `--non-interactive`**. Cómo verificar: ejecutar `./installer uninstall --non-interactive` ⇒ no pide prompt y termina con exit 0.

- [ ] **DoD-26 Estado persistente mínimo**: `installed_version`, `platform`, timestamps y `snapshots[]`. Cómo verificar: ejecutar `./installer doctor --json` ⇒ contiene esos campos y no vacíos.
- [ ] **DoD-27 Manifiesto de archivos gestionados con hash** (rollback/uninstall). Cómo verificar: tras `install`, ejecutar `./installer doctor --json` ⇒ contiene `managed_files[]` con `path` + `sha256` (o equivalente).
- [ ] **DoD-28 Historial de snapshots consultable**. Cómo verificar: ejecutar `./installer rollback --list` ⇒ imprime lista ordenada con IDs y fechas, exit 0.

- [ ] **DoD-29 Logs persistentes en managed surface + ruta mostrada**. Cómo verificar: ejecutar `./installer install` ⇒ imprime ruta del log bajo `~/.opencode/` y el archivo existe.
- [ ] **DoD-30 Logs correlacionados por ejecución (`run_id`)**. Cómo verificar: ejecutar `./installer doctor` ⇒ muestra `run_id` y el mismo `run_id` aparece en el log de esa ejecución.

- [ ] **DoD-31 Documentación mínima del instalador** (Quickstart Linux/macOS + Windows `.ps1` doctor-only + comandos/flags + managed surface + non-goals). Cómo verificar: abrir README del instalador ⇒ contiene ejemplos ejecutables y sección “State/Paths” con `~/.opencode/` y “no dotfiles por defecto”.

- [ ] **DoD-32 Git flow aplicado** (branch feature/fix + PR). Cómo verificar: `git branch --show-current` ⇒ prefijo `feature/` o `fix/` (o convención acordada) y existe PR asociado.
- [ ] **DoD-33 Conventional commits aplicados**. Cómo verificar: `git log --oneline -n 20` ⇒ la mayoría inicia con `feat:`, `fix:`, `docs:`, `chore:` (u otro set acordado) de forma consistente.