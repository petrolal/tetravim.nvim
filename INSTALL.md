# TetraVim Neovim Installation Guide

## Quick Start

```bash
git clone https://github.com/petrolal/tetravim.nvim.git ~/tetravim.nvim
cd ~/tetravim.nvim
bash bootstrap.sh
nvim
```

That's it — plain `nvim` now launches TetraVim. TetraVim always lives at
`~/tetravim.nvim`; `bootstrap.sh` symlinks `~/.config/nvim` to it. If you
clone (or `bash bootstrap.sh`) from anywhere else, the script moves the repo
to `~/tetravim.nvim` on first run and re-execs itself from there — so
`~/tetravim.nvim` is always the canonical, only supported install path.
`bootstrap.sh` is idempotent; re-run it any time to refresh plugins/tools
(e.g. after `git pull`).

---

## Before you run it

`bootstrap.sh` checks for these but does **not** install them (package names
and preferred versions vary too much per distro):

| Tool | Why | Install |
| --- | --- | --- |
| **Neovim ≥ 0.11** | Hard requirement — `init.lua` refuses to load on older versions (`vim.lsp.config`/`enable`, `vim.diagnostic.jump`, `winborder`). | `brew install neovim` / `sudo apt install neovim` / `sudo pacman -S neovim` |
| **git** | `lazy.nvim` fetches every plugin over git. | usually preinstalled; otherwise your package manager |
| **ripgrep (`rg`)** | Telescope live-grep, snacks.picker — `:checkhealth tetravim` marks this REQUIRED. | `brew install ripgrep` / `sudo apt install ripgrep` / `sudo pacman -S ripgrep` |
| **A C compiler** (`cc`/`gcc`/`clang`) and **`make`** | Tree-sitter parser compilation, `telescope-fzf-native`. | Xcode CLT / `build-essential` / `base-devel` |
| **A JDK (21)** | Required to actually *launch* `jdtls` (Java) and Metals (Scala) — Mason installs the language servers themselves, but not a JVM to run them on. | `sdk install java` / `brew install openjdk` / `sudo apt install openjdk-21-jdk` / `sudo pacman -S jdk-openjdk` |
| **A Nerd Font (v3.0+)**, set as your **terminal's** font | Dashboard, statusline, bufferline, winbar, which-key, file-tree and completion menus all render Nerd Font glyphs. | pick one from [nerdfonts.com](https://www.nerdfonts.com/font-downloads) |
| **A true-color terminal** | `termguicolors` is on by default. | WezTerm, Kitty, Alacritty, Ghostty, modern iTerm2 / Windows Terminal |

`bootstrap.sh` prints a `⚠` for anything above it can't find, with the same
fix hints, so you don't need to check by hand — just read its output.

---

## What `bootstrap.sh` actually does

Each numbered section is independent and safe to re-run:

0a. Guarantees the repo lives at `~/tetravim.nvim` — if `bootstrap.sh` is run
    from anywhere else, it moves the repo there (backing up any existing
    `~/tetravim.nvim` as `tetravim.nvim.backup.<timestamp>`) and re-execs
    itself from the new location.
0. Verifies Neovim exists and is `>= 0.11`; checks the prerequisites above.
1. Clears the Neovim and Tree-sitter caches (avoids stale-lockfile issues).
2. Symlinks `~/tetravim.nvim` to `~/.config/nvim` (backing up any existing
   real directory as `nvim.backup.<timestamp>`), then runs the headless
   provisioning pipeline (`tetravim.core.setup.run()`): `lazy.nvim` plugin
   sync, Mason LSP/DAP/linter tools, JVM framework LSP jars, Tree-sitter
   parsers.
3. Installs npm-based tools if `npm` is present: the Node provider, plus
   `prettier`, `sonarqube-scanner`, `tree-sitter-cli`, `@mermaid-js/mermaid-cli`.
4. Installs `yamlfmt` via `go install` if `go` is present.
5. Installs `pynvim` (Python provider) if `python3`/`python` is present.
6. Installs the `regex` Tree-sitter parser.
7. Best-effort installs PDF/LaTeX preview tools (`gs`, `tectonic`/`pdflatex`)
   via your system package manager.
8. Best-effort installs `grpcurl`.
9. Best-effort installs `osv-scanner` (CVE scanning).
10. Installs core CLI tools (`rg`, `jq`, `curl`, `unzip`) via your system
    package manager if missing.
11. Installs `scalafmt`/`scalastyle` via Coursier (`cs`), if present.
12. Installs `async-profiler` (JVM sampling profiler) via package manager or,
    failing that, downloads the upstream release tarball.

Everything from step 3 onward degrades gracefully: a missing tool for that
step just prints a `⚠` with a manual install command and the script moves on
— nothing after step 2 is required for TetraVim to start and work with Java.

---

## Troubleshooting

**"Neovim not found" / "too old"**
```bash
# macOS
brew install neovim
# Ubuntu/Debian
sudo apt install neovim
# Arch
sudo pacman -S neovim
# Fedora
sudo dnf install neovim
```

**Plugin sync failed** — run inside Neovim:
```vim
:Lazy sync
```

**Something else looks off** — run `:checkhealth tetravim`; it re-probes
every dependency this doc lists and points at the exact fix.

---

## Uninstall

```bash
rm ~/.config/nvim   # it's a symlink, not the real directory
rm -rf ~/tetravim.nvim
# restore a previous config if bootstrap.sh backed one up:
mv ~/.config/nvim.backup.* ~/.config/nvim
```

---

## Getting Help

- GitHub Issues: https://github.com/petrolal/tetravim.nvim/issues
- Documentation: https://github.com/petrolal/tetravim.nvim
