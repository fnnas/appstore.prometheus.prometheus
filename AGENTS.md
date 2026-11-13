# Repository Guidelines

## Project Structure & Module Organization

This repository packages upstream Prometheus for fnOS; it does not contain the Prometheus Go source.

- `app/`: runtime configuration templates and desktop integration under `app/ui/`. Packaging downloads the `prometheus` executable here.
- `prometheus/`: package manifest, icons, Bash lifecycle hooks in `cmd/`, permissions and shares in `config/`, installation wizard, and translations in `i18n/`.
- `.github/workflows/main.yml`: version settings and manual build/release orchestration. `package_amd64.yml` and `package_arm64.yml` build architecture-specific archives.
- `ui/`: packaging placeholder; preserve its tracked `.gitkep` file.

## Build, Test, and Development Commands

Run Linux commands in Linux or WSL with Bash and GNU tar available.

- Trigger **Test Release** in GitHub Actions with `make_release=false` to build both architectures without publishing a release.
- `bash -n prometheus/cmd/main`: check the runtime hook's syntax; repeat for changed lifecycle hooks.
- `promtool check config app/prometheus.yml.template`: validate scrape configuration using `promtool` from the matching upstream release.
- `tar -tzf <package>.tgz`: inspect the generated package and its `prometheus/` root.

For local packaging, reproduce the architecture workflow in a disposable directory: download the upstream binary, substitute manifest placeholders, create `app.tgz` from `app ui`, then archive `prometheus/`. Keep generated binaries and archives out of commits.

## Coding Style & Naming Conventions

Match surrounding formatting: Bash generally uses four-space indentation; scrape YAML uses two spaces. Preserve manifest placeholders such as `this_pack_version` and existing `wizard_*` field names. Use LF line endings for shell scripts. No formatter or lint configuration is currently provided.

### Localization

Maintain `en`, `zh-CN`, `ja-JP`, `zh-TW`, `de-DE`, `fr-FR`, `es-ES`, `ko-KR`, `ru-RU`, and `it-IT` under `prometheus/i18n/`. Keep section names and translation keys synchronized with `en`, using UTF-8 files without extensions. Translate package descriptions and all installation wizard text; preserve technical identifiers, paths, URLs, and HTML markup. Validation messages must reflect the wizard's actual ASCII alphanumeric restrictions.

## Testing Guidelines

There is no automated test suite or coverage threshold. For upstream-version-only updates, verify the latest official stable release and synchronize version settings; no local build is required. Validate the diff and version consistency instead. For packaging logic changes, validate changed scripts and configuration and build both architectures. On fnOS, check installation, start/stop/status, port 9090, authentication, upgrade behavior, and persistence. Distinguish local checks from device testing.

## Commit & Pull Request Guidelines

Use `update(package):<中文变更概述>` for package update subjects. State the actual upstream version and summarize related changes when applicable; keep the title concise. After a blank line, write the commit body in Chinese:

- Start with one or more concise paragraphs explaining the overall change, its purpose, and resulting behavior.
- Follow with changed file paths on separate lines; group closely related files when appropriate.
- Under each path, use bullets formatted as `- <变更主题> 新增/修改/修复/删除：<具体说明>`, selecting the applicable action. Describe only actual changes.
- Scale detail to the change; avoid repeating the overview, copying unrelated examples, or adding a separate validation section to the commit body.

PRs should describe changes, affected architectures, validation performed, and related issues; include screenshots for wizard or desktop changes.

## Configuration & Data Safety

Preserve existing shared `prometheus.yml` and TSDB data during lifecycle changes. Web authentication lives in `${TRIM_PKGETC}/prometheus-web.yml`; never commit credentials or generated password hashes. Keep upstream and package versions coordinated in `main.yml`.
