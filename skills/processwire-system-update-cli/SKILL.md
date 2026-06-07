---
name: "processwire-system-update-cli"
description: "Upgrade ProcessWire modules first and core second through DDEV CLI"
version: 4
created: "2026-06-07"
updated: "2026-06-07"
---
## When to Use
Use when updating a DDEV-based ProcessWire project's modules and core through CLI automation.

## Procedure
1. Start by scanning for available updates before changing files. Use `ProcessWireUpgradeCheck` through `ddev php` to list outdated modules and core branches: `modules()->get('ProcessWireUpgradeCheck')->getModuleVersions(true, true)` and `getCoreBranches(false, true)`.
2. Include a compatibility check in the initial scan. Inspect each update's requirements from `ProcessWireUpgradeCheck`/module-directory data (`requiresVersions`) and, when ZIP/source files are available, the module's public `getModuleInfo()` array `requires` property. Compare requirements against the current `PHP_VERSION` and `config()->version`.
3. Decide update order from compatibility results:
   - If all module updates satisfy the current PHP and ProcessWire versions, follow the normal **modules before core** paradigm.
   - If one or more module updates require a newer PHP version than DDEV currently provides, warn the user clearly and stop for a PHP/DDEV version decision before installing those modules.
   - If one or more module updates require a newer ProcessWire version than currently installed, explain that this is a compatibility exception and suggest updating ProcessWire core first, then returning to module updates.
4. If the scan reports Pro/paid modules, stop and ask the user for local ZIP paths for each Pro module before proceeding. Do not assume the paths are the same across projects. Confirm the ZIPs exist and inspect their top-level folder/module names before installation.
5. Start DDEV and create a DB snapshot with `ddev snapshot --name before-system-update-$(date +%Y%m%d%H%M%S)`.
6. Always update modules before ProcessWire core when compatibility allows it. Each module update must be committed separately with a conventional commit.
7. For module ZIPs, use `wire/modules/Process/ProcessModule/ProcessModuleInstall.php` and `ProcessModuleInstall::unzipModule($tempZip, config()->paths->siteModules . $module . '/')`, then `modules()->refresh()` and `modules()->resetCache()`. Copy paid ZIPs into a project/cache temp path first because `unzipModule()` deletes the ZIP it extracts.
8. For public modules, download the ZIP quietly into `site/assets/cache/module-update-zips/`, install via the same `ProcessModuleInstall::unzipModule()` flow, verify version with `getModuleInfoVerbose()`, then commit only that module directory.
9. After all modules report no outdated modules, update ProcessWire core from the dev branch ZIP. Replace `wire/` from the extracted archive, update `index.php` when its PROCESSWIRE index version changes, update `.htaccess` only after reviewing diffs, and update `composer.json` package metadata if changed.
10. Verify via `ddev php` bootstrap: `config()->version`, module versions, and `count($check->getModuleVersions(true,true)) === 0`. Smoke test frontend and the admin URL from `config()->urls->admin`.
## Pitfalls
- Do not blindly update ProcessWire core before modules. The preferred order is modules first, then core, but compatibility requirements can create an exception.
- Do not blindly update modules first if their `requires`/`requiresVersions` says they need a newer ProcessWire core or PHP runtime. Warn about PHP requirements; suggest core-first only when a module update requires newer ProcessWire.
- Do not group module updates in one commit.
- Do not pass original paid-module ZIP paths directly to `unzipModule()`; it deletes the ZIP after extraction and container PHP cannot see arbitrary host `/home/...` paths unless copied into the project.
- `ProcessModuleInstall::downloadModuleFromUrl()` requires `$config->moduleInstall('download', ...)` to be enabled, so the smoother CLI path is to download ZIPs quietly with shell/curl into `site/assets/cache/module-update-zips/`, then call `ProcessModuleInstall::unzipModule()` on that local project path.
- When using `ddev php -r` one-liners, avoid unescaped `$variables` in double-quoted shell strings; either single-quote the PHP code or escape `$`. Bash stripped `$m` once and caused a PHP parse error.
- `modules()->getModuleInfo()` may show stale/cached or raw numeric versions right after replacing files. Final working verification is: `modules()->resetCache(); modules()->refresh(); modules()->getModuleInfoVerbose($module); modules()->formatVersion($info['version'])`.
- For Pro modules with child modules (`FieldtypeRepeaterMatrix` installs `InputfieldRepeaterMatrix`, `FieldtypeTable` installs `InputfieldTable`, `ProMailer` installs `ProcessProMailer`), verify both parent and child module versions.
- `ProcessWireUpgradeCheck` is useful for detection only; `ProcessWireUpgrade` links module updates to the admin UI and is not the CLI installer. The final working CLI installer was `ProcessModuleInstall::unzipModule()` plus `modules()->refresh()`.
- Core dev ZIP reorganized many `wire/core/*.php` files into subdirectories, so a clean `rsync -a --delete extracted/wire/ wire/` is preferable to copying over existing files without deletion.
- Review `index.php` and `.htaccess` diffs separately during core update. In this upgrade, `index.php` needed updating from PROCESSWIRE index `300` to `303`; `.htaccess` had the same `@htaccessVersion 301` but included a security-header comment/change, so it was intentionally copied after review.
- Do not assume the admin URL is `/processwire/`; read it with `config()->urls->admin` and smoke test that path.
- `ddev snapshot list` is wrong for this DDEV version; use `ddev snapshot --list`.
## Verification
1. The initial report includes update availability plus compatibility status for PHP and ProcessWire requirements.
2. `git status --short --branch` is clean after commits.
3. `ddev php` reports expected ProcessWire and module versions.
4. `ProcessWireUpgradeCheck->getModuleVersions(true,true)` returns zero items.
5. Frontend and the admin URL from `config()->urls->admin` return HTTP 200.
6. A pre-upgrade DDEV snapshot exists and commit history shows one commit per module followed by the core commit, unless a clearly documented compatibility exception required core first.