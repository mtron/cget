# cget

A bash wrapper around `curl` with wget-like defaults.

Requires **bash ≥ 5** and **curl ≥ 7.73**

## Install


```bash
mkdir -p $HOME/work && cd $HOME/work
git clone https://github.com/mtron/cget.git
```
Load the script as a bash function by adding this one-liner to your ~/.bashrc :

```bash
# cget - wget like wrapper for curl
cget() { "$HOME/work/cget/cget" "$@"; }
```

Then `source ~/.bashrc` or open a new shell.

## Usage

```bash
# Save into ~/Downloads, name from Content-Disposition (URL Fallback)
cget https://example.com/file.tar.gz

# Explicit output file / stdout
cget -O ./file.tar.gz https://example.com/file.tar.gz
cget -O - https://example.com/file.tar.gz | tar -tzf -

# Directory prefix (wget -P)
cget -P /tmp https://example.com/file.tar.gz

# Resume is automatic when the target file already exists; -c forces it
cget https://example.com/big.iso
cget -q https://example.com/file.tar.gz
```

Ctrl+C stops the transfer immediately (curl is killed; no retry after abort).

### Options

| Option | Meaning |
|--------|---------|
| `-O`, `--output-document=FILE` | Write to `FILE` (`-` = stdout) |
| `-P`, `--directory-prefix=DIR` | Save under `DIR` |
| `-c`, `--continue` | Force resume (`curl -C -`; also auto if target exists) |
| `-q`, `--quiet` | No status line / curl noise |
| `-nv`, `--no-verbose` | Status line only (default behaviour) |
| `-h`, `--help` / `-V`, `--version` | Help / version |

Unknown flags are forwarded to `curl`. Use `--` to pass curl-only options unambiguously:

```bash
cget -- --retry-all-errors --max-time 600 https://example.com/file.bin
```

## Logic

- Default destination is `~/Downloads`. Customize via `-P <dir>`.
- Filename comes from `Content-Disposition` when present, otherwise URL basename.
- If the target file already exists and is non-empty, resume with `curl -C -` automatically; the status line shows overall file progress (not only the remaining Range).
- Built-in curl defaults: `-L --fail --remote-time --retry 3 --connect-timeout 10`.
- The status line is parsed from `curl --progress-meter` (works when stderr is not a TTY).
- Ctrl+C kills curl immediately (does not wait for the next progress update or a retry).
