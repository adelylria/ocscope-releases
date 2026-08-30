# ocscope

**Languages:** [🇪🇸 Español](README.md) | [🇬🇧 English](README.en.md)

![ocscope in the main view](screenshots/overview.png)

**ocscope** is a terminal tool to observe and diagnose applications deployed on
OpenShift. It brings together Pod status, events, diagnostics, logs, metrics,
and workload state in a fast and readable interface.

> **Read-only:** ocscope does not apply changes, delete resources, scale
> workloads, restart Pods, or execute commands inside containers.

> [!NOTE]
> This page contains only published binaries. The project's source code is
> maintained in a separate repository.

## Screenshots

![Overview](screenshots/overview.png)

![Real-time logs](screenshots/logs.png)

![Log wall](screenshots/log-wall.png)

![Metrics](screenshots/metrics.png)

<!-- [EDIT] You can remove screenshots or add a short usage GIF. -->

## Features

- Pod list sorted to show problematic states first.
- Diagnosis of common issues like `OOMKilled`, probes, images, scheduling,
  volumes, networking, and `CrashLoopBackOff`.
- Owner workload state and replica availability.
- Current CPU and memory compared with requests and limits.
- Metrics history when the cluster exposes Prometheus/Thanos and your account
  has sufficient permissions.
- Logs from the current and previous execution, without mixing them.
- Current logs via persistent streaming with automatic reconnection and polling
  fallback.
- Container selector, including init containers.
- `LOG ONLY` mode for reading and copying logs with more space.
- 2 to 6 Pod wall with filtering, focus, scroll, pause, and independent
  execution per panel.
- Project switching without modifying the persistent `oc` context.
- Interface available in Spanish and English.

## Requirements

- macOS or Windows.
- OpenShift CLI (`oc`) installed and available in the `PATH`.
- A valid session on the cluster:

  ```bash
  oc login --web
  oc whoami
  oc project -q
  ```

- A modern terminal with UTF-8 support. On Windows, Windows Terminal is
  recommended.

You do not need to install Go to use the published binaries.

## Download

Go to the [Releases](https://github.com/adelylria/ocscope-releases/releases)
section and download the package for your system:

| System | Recommended file |
|---|---|
| macOS Apple Silicon | `ocscope-VERSION-darwin-arm64.tar.gz` |
| macOS Intel | `ocscope-VERSION-darwin-amd64.tar.gz` |
| macOS Intel or Apple Silicon | `ocscope-VERSION-darwin-universal.tar.gz` |
| Windows x64 | `ocscope-VERSION-windows-amd64.zip` |
| Windows ARM | `ocscope-VERSION-windows-arm64.zip` |

Replace `VERSION` with the published version, for example `0.15.0`.

## Installation on macOS

1. Download the recommended `darwin-universal` package for most current
   systems.
2. Extract it:

   ```bash
   tar -xzf ocscope-VERSION-darwin-universal.tar.gz
   cd ocscope-VERSION-darwin-universal
   ```

3. Run the instructions in `INSTALL.txt` or install the binary in a folder
   from your `PATH`:

   ```bash
   mkdir -p "$HOME/.local/bin"
   install -m 755 ocscope "$HOME/.local/bin/ocscope"
   ```

4. If you use `zsh` and that folder is not in your `PATH`:

   ```bash
   echo 'export PATH="$HOME/.local/bin:$PATH"' >> "$HOME/.zshrc"
   export PATH="$HOME/.local/bin:$PATH"
   ```

5. Verify the installation:

   ```bash
   ocscope --version
   ```

macOS packages are not signed or notarized. macOS may ask you to authorize the
first run from **System Settings → Privacy and security**.

## Installation on Windows

1. Download the `windows-amd64` ZIP for most systems or `windows-arm64` for
   Windows on ARM.
2. Extract the ZIP.
3. Open PowerShell in the extracted folder and run:

   ```powershell
   powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\install.ps1
   ```

The installer places ocscope in `%LOCALAPPDATA%\Programs\ocscope` and adds that
folder to the user's `PATH`. Open a new terminal and verify:

```powershell
ocscope --version
```

Windows binaries are not signed; SmartScreen may show a warning. Continue only
if you recognize the package source.

## First run

With a valid `oc` session, run:

```bash
ocscope
```

If you have access to multiple projects, ocscope will show a selector on
startup. You can also specify one directly:

```bash
ocscope --project my-project
```

To test the interface without connecting to OpenShift:

```bash
ocscope --demo
```

## Updates

Published versions check for updates on startup. When a new version is
available, the application shows a notice and the command you need to run:

```bash
ocscope --update
```

You can also check manually without downloading anything:

```bash
ocscope --check-update
```

The download is verified with `checksums.txt` before replacing the executable.
The replacement is transactional: if it fails, the installed version is
preserved.

To disable the check on startup:

```bash
ocscope --no-update-check
```

Development binaries do not perform auto-updates.

## Main shortcuts

| Key | Action |
|---|---|
| `↑` / `↓`, `j` / `k` | Select Pod |
| `1`–`5`, `Tab` | Change view |
| `c` | Select container |
| `v` | Toggle previous/current execution |
| `f` | Open or close `LOG ONLY`; in wall, maximize/restore |
| `a` | Open wall with filtered Pods |
| `Tab` / `Shift+Tab` | Switch active wall panel |
| `Space` | Pause or resume active log |
| `Page Up` / `Page Down` | Scroll log |
| `Ctrl+U` / `Ctrl+D` | Alternative scrolling |
| `/` | Filter Pods |
| `p` | Switch project |
| `r` | Refresh now |
| `?` | Show help |
| `q` | Quit |

On a MacBook, `fn`+`↑` and `fn`+`↓` are equivalent to `Page Up` and `Page Down`.

## Download integrity

Each release includes a `checksums.txt` file with SHA-256 hashes. You can verify
a package from macOS or Linux like this:

```bash
grep 'ocscope-VERSION-darwin-universal.tar.gz' checksums.txt \
  | shasum -a 256 -c -
```

In PowerShell:

```powershell
Get-FileHash .\ocscope-VERSION-windows-amd64.zip -Algorithm SHA256
```

Compare the result with the corresponding line in `checksums.txt` published in
the same release.

## Optional configuration

ocscope uses the active `oc` configuration by default. It also supports these
options:

```bash
ocscope --project my-project
ocscope --context my-context
ocscope --kubeconfig /path/to/kubeconfig
ocscope --language es
ocscope --refresh 5s
```

<!-- [EDIT] Add environment variables or values recommended by your team here. -->

## Troubleshooting

### `ocscope` cannot find `oc`

Verify that `oc` is installed and available:

```bash
oc version --client
```

### Historical metrics are not showing

Current metrics and history depend on permissions and the cluster's monitoring
configuration. The main view continues working even if Thanos or
`metrics.k8s.io` are unavailable.

### I cannot update the executable

Verify that the user has write permissions on the folder where ocscope is
installed. On Windows, close any process using it and open a new terminal after
updating.

### macOS or Windows show a security warning

Distributed binaries are not yet signed. Verify that the file comes from the
official release and validate its checksum before authorizing it.

## Privacy and security

ocscope uses the `oc` session and kubeconfig to query the cluster. Tokens are
not displayed or saved in release packages. The application does not print
secrets, variable values, or Secrets contents.

<!-- [EDIT] Replace or expand this section with your privacy policy. -->

## License

<!-- [EDIT] Indicate the license and link to the legal text here. -->

This software is distributed under the terms of **[LICENSE NAME]**.
See [`LICENSE`](LICENSE) for more information.

## Contact and support

<!-- [EDIT] Replace these links with official channels. -->

- Issues: [Issues](https://github.com/adelylria/ocscope-releases/issues)
- Releases: [Releases](https://github.com/adelylria/ocscope-releases/releases)

---

**Documented version:** `[VERSION]`  
**Last updated:** `[DATE]`
