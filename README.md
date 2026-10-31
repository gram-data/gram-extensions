# gram-extensions

The registry of extensions for the [`gram` CLI](https://github.com/gram-data/tree-sitter-gram). `gram extension list` and `gram extension install <name>` read [`registry.toml`](registry.toml) from this repository's `main` branch.

This is a separate repository on purpose. The CLI lives in tree-sitter-gram, and extensions such as `gram-ontology` depend on tree-sitter-gram's crates. Keeping the registry here means listing a new extension never needs a change to the grammar repo, and the repositories do not depend on each other in a cycle: the CLI knows only this file's URL, and this file only names extension repositories.

## Format

```toml
[[extension]]
name = "lsp"                                    # unique, matches [a-z][a-z0-9-]*
description = "Language server protocol support"
github = "gram-data/tree-sitter-gram"           # owner/repo that publishes releases
bin = "gram-lsp"                                # base binary name, no platform suffix
```

An extension is installed from the GitHub Releases of `github`. Release artifacts must be named `<bin>-<target-triple>.<ext>` (`tar.gz`, or `zip` on Windows) for the targets the CLI supports. The full contract is in tree-sitter-gram's [`specs/007-unified-gram-cli/contracts/extension-registry.md`](https://github.com/gram-data/tree-sitter-gram/blob/main/specs/007-unified-gram-cli/contracts/extension-registry.md).

## Versions

[`versions.toml`](versions.toml) records the latest release of every entry, so the CLI can show available versions with one request instead of one GitHub API call per extension. It is generated, not edited: the [Update versions](.github/workflows/versions.yml) workflow runs daily, on demand, and whenever `registry.toml` changes, and commits only when a version changed. `updated_at` is the time of the last change. An entry whose release the workflow cannot read, such as one in a private repository, keeps its previous version.

## Adding an extension

Open a pull request that adds an `[[extension]]` entry. The extension's repository should already have a release with artifacts following the naming convention above.

## Testing a change

Point the CLI at a copy of the registry before publishing it:

```bash
GRAM_REGISTRY_URL=https://raw.githubusercontent.com/<you>/gram-extensions/<branch>/registry.toml gram extension list --available
```
