# cget

A small bash wrapper around `curl` with wget-like defaults.

Requires **bash ≥ 5** and **curl ≥ 7.73**

## Install


```bash
mkdir -p $HOME/work/cget && cd $HOME/work
git clone https://git.glasn.net/mtron/cget.git

Load the script as ~/.bashrc - function

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
cget -- --retry-all-errors --max-time 600 https://example.com/file.bin
```

## Logic

- Default destination is `~/Downloads`; 
- filename comes from `Content-Disposition` when present, otherwise URL basename.
- Built-in curl defaults for downloads: `-L --fail --remote-time --retry 3 --connect-timeout 10`.
- The status line is parsed from `curl --progress-meter` (works when stderr is not a TTY)
