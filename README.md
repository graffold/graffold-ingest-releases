# graffold-ingest

Standalone binary releases of the **graffold-ingest** CLI — turn documents,
literature, and reasoning into knowledge graphs, and query hosted graphs
remotely.

These are compiled binaries. No Python install required, and the source is not
included (the CLI source is private).

---

## Install

```sh
curl -fsSL https://github.com/graffold/graffold-ingest-releases/releases/latest/download/install.sh | sh
```

Installs to `~/.local/bin/graffold-ingest`. If that directory isn't on your
`PATH`:

```sh
export PATH="$HOME/.local/bin:$PATH"      # add to ~/.zshrc or ~/.bashrc
```

**Supported platforms:** macOS (Apple Silicon + Intel), Linux (x86_64).

### Verify the install

```sh
graffold-ingest --version
graffold-ingest --help
```

### Install a specific version

```sh
GRAFFOLD_VERSION=v0.4.0 sh -c "$(curl -fsSL https://github.com/graffold/graffold-ingest-releases/releases/latest/download/install.sh)"
```

---

## Query hosted knowledge graphs

Get an API key from the **/kg** page on the webapp (requires a paid tier), then
store it once:

```sh
graffold-ingest login <your-api-key>
# or set it in the environment (overrides the stored key):
export GRAFFOLD_API_KEY=<your-api-key>
```

> The key is sent as a bearer header on each request — it is not a session.

Then query any hosted graph:

```sh
graffold-ingest kg graphs                              # list available graphs
graffold-ingest kg stats -g etec                       # entity/relationship counts
graffold-ingest kg coverage "NetB,sialidase" -g etec   # which entities exist
graffold-ingest kg validate "F18-ETEC,F41 antigen" -g etec   # score candidate targets
graffold-ingest kg evidence "heat-labile enterotoxin" TARGETS_DISEASE ETEC -g etec
graffold-ingest kg contradictions -g master            # contested claims
graffold-ingest kg prior-knowledge "ETEC" -g etec -o brief.md
graffold-ingest kg gaps -g etec                        # targets lacking validated evidence
graffold-ingest kg predictions -g etec --min-shared 3  # structural inference
graffold-ingest kg trajectory "F18-ETEC" -g etec       # a target's history
```

Available graphs: `alltech`, `elanco`, `zoetis`, `etec`, `master`,
`massive-ckd`. Add `-f json` to any command for machine-readable output.

---

## Build your own local graph

```sh
graffold-ingest ingest <atlas-program-dir>       # build a KG from a program dir
graffold-ingest ingest-corpus queries.txt        # build from a literature query list
graffold-ingest harmonize <graph-dir>            # collapse fragmented entities
graffold-ingest contradictions --graph <graph-dir>   # contested claims (local)
graffold-ingest query "cryptosporidiosis" --graph <graph-dir>
graffold-ingest catalog ~/.graffold/parquet      # inventory all local graphs
```

Run `graffold-ingest <command> --help` for options on any command.

---

## Environment variables

| Variable | Purpose | Default |
|----------|---------|---------|
| `GRAFFOLD_API_KEY` | Hosted-API bearer key (overrides stored) | — |
| `GRAFFOLD_API_URL` | Hosted API base | `https://api.graffold.com` |
| `PARQUET_DIR` | Local graph store | `~/.graffold/parquet` |

---

## Manual download

Prefer not to pipe to `sh`? Grab the tarball for your platform from the
[latest release](https://github.com/graffold/graffold-ingest-releases/releases/latest),
unpack it, and put the `graffold-ingest` launcher on your `PATH`:

| Platform | Asset |
|----------|-------|
| macOS Apple Silicon | `graffold-ingest-aarch64-apple-darwin.tar.gz` |
| macOS Intel | `graffold-ingest-x86_64-apple-darwin.tar.gz` |
| Linux x86_64 | `graffold-ingest-x86_64-unknown-linux-gnu.tar.gz` |

```sh
tar -xzf graffold-ingest-<platform>.tar.gz
./graffold-ingest/graffold-ingest --help
```

Each release lists a SHA-256 for every asset — verify before running if you
like:

```sh
shasum -a 256 graffold-ingest-<platform>.tar.gz   # compare to the release page
```

> **Note:** the auto-attached "Source code (zip/tar.gz)" on each release are
> boilerplate for this releases repo only — they do not contain the CLI source.
> Use the platform tarballs above.
