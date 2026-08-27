## Structure

- When any tool is added, moved, or removed, update README.md to reflect the change.
- Tools live in `tools/` or in sibling groups (`tools-wg21/`) and subdirectory groups (`tools/code/`, `tools/voice/`). Retired tools live in the group's `retired/` subdirectory (e.g., `tools-wg21/retired/`), and their README entry moves to the matching Retired section rather than being deleted.
- A tool reaches curl-installer users only once it is listed in `install.sh`, by its path relative to the repo root. Moving or renaming a tool means updating that list and its README link in the same commit.
- The plugin manifest `.claude-plugin/plugin.json` registers commands by directory, not by file: each entry in its `commands` array (e.g., `./tools/code/`) loads every `.md` directly inside that directory, non-recursively. A new tool in an already-listed directory needs no manifest change. A new tool directory means adding its entry to the `commands` array by hand in the same commit.
- Do not run `./scripts/sync_plugin_manifest.py`. It predates directory registration (#8) and rewrites the manifest back to a per-file list, silently undoing that change.
- Before committing a change to `install.sh` or the manifest, run `claude plugin validate . --strict`, which catches a manifest entry pointing at a path that is not in the tree. There is no CI, so this check is yours to run.
- Dossiers live in `dossiers/`. These are composite behavioral models - aggregate profiles of populations, not individuals.
- Every directory that contains tools or dossiers also contains an `images/` subdirectory for their paired images.
- Every `.md` file pairs with `images/<name>.png` in the same directory group. The image filename always matches the `.md` filename (minus extension).
- Directory-style tools (e.g., `voice/voice.md`) key on the parent directory name: `voice/` pairs with `images/voice.png`. Sub-tools inside a directory may have their own images in the same `images/`.
- Skills are directories holding a `SKILL.md` plus the scripts it calls. A skill is registered by adding its directory to the `SKILLS` array in `install.sh`, and installs to `~/.claude/skills/` and `~/.cursor/skills/` rather than `~/.claude/commands/`. Unlike a command, the whole directory ships, so anything the prompt calls must live inside it.

## Image invariant

The image travels with its file. On create, move, rename, delete, or retire - both files move together, never one without the other. Images always live in `images/` within the file's directory group, never loose alongside the `.md` files.

The `<img src>` in every tool or dossier is a relative path to `images/<name>.png`. Never an absolute URL. Never a bare filename without `images/`.

If the paired image is missing, stop and ask. Do not proceed with a lone file.
