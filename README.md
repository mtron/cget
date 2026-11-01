# cget

A small bash wrapper around `curl` with wget-like defaults and a live download status line.

```
[Download]   1.9% of   73.91MiB at   54.19KiB/s (50 KiB/s Ø)    ETA 22:50
```

Requires **bash ≥ 5** and **curl ≥ 7.73** (developed against bash 5.3.9 and curl 8.18.0).

## Install (bashrc)

Prefer keeping the script in this repo and putting it on your `PATH`, or wrapping it in a function:

```bash
# ~/.bashrc — one-liner (PATH)
export PATH="$HOME/work/cget:$PATH"
```

```bash
# ~/.bashrc — one-liner (function; no PATH change)
cget() { "$HOME/work/cget/cget" "$@"; }
```

Then `source ~/.bashrc` or open a new shell. The script is `~/work/cget/cget` (executable).

## Usage

```bash
# Save into ~/Downloads (created if missing), name from URL / Content-Disposition
cget https://example.com/file.tar.gz

# Explicit output file / stdout
cget -O ./file.tar.gz https://example.com/file.tar.gz
cget -O - https://example.com/file.tar.gz | tar -tzf -

# Directory prefix (wget -P)
cget -P /tmp https://example.com/file.tar.gz

# Resume, quiet
cget -c https://example.com/big.iso
cget -q https://example.com/file.tar.gz
```

### Options

| Option | Meaning |
|--------|---------|
| `-O`, `--output-document=FILE` | Write to `FILE` (`-` = stdout) |
| `-P`, `--directory-prefix=DIR` | Save under `DIR` |
| `-c`, `--continue` | Resume (`curl -C -`) |
| `-q`, `--quiet` | No status line / curl noise |
| `-nv`, `--no-verbose` | Status line only (default behaviour) |
| `-h`, `--help` / `-V`, `--version` | Help / version |

Unknown flags are forwarded to `curl`. Use `--` to pass curl-only options unambiguously:

```bash
cget -- --retry 3 --connect-timeout 10 https://example.com/file.bin
```

## How it works

- Default destination is `~/Downloads` with `curl -O -J --output-dir` (URL basename, or `Content-Disposition` when present).
- The status line is parsed from `curl --progress-meter` (forced on so it works when stderr is not a TTY) and rewritten into the `[Download] … Ø … ETA` format.
