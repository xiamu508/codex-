# Codex History Provider Restore

Restore local Codex conversation history visibility after switching API accounts or model providers.

This Codex skill helps recover old conversations that disappear from the sidebar after account/provider changes. It finds the current chat's `model_provider`, then relabels local Codex session metadata and the SQLite thread index so previous conversations appear under the current account framework.

## GitHub Description

Short repository description:

```text
Codex skill to restore local conversation history visibility after switching API accounts or providers.
```

Suggested topics:

```text
codex, codex-skill, openai-codex, conversation-history, account-switching, sqlite, jsonl
```

## What It Changes

The skill changes only provider labels:

- `~/.codex/sessions/**/*.jsonl`
- `~/.codex/archived_sessions/*.jsonl`
- `~/.codex/state_5.sqlite`

It does not rewrite message bodies. Before applying changes, the script creates backups of every changed JSONL file and `state_5.sqlite`.

## Install

Clone or download this repository, then copy the skill folder into your Codex skills directory:

```bash
mkdir -p ~/.codex/skills
cp -R codex-history-provider-restore ~/.codex/skills/
```

If you downloaded a zip, unzip it first and make sure this path exists:

```text
~/.codex/skills/codex-history-provider-restore/SKILL.md
```

Restart Codex if the skill does not appear immediately.

## Unlock / Trigger In Codex

Invoke the skill explicitly in Codex:

```text
Use $codex-history-provider-restore to restore my old Codex conversations into the current account framework.
```

For better current-chat detection, include a unique sentence from the current chat:

```text
Use $codex-history-provider-restore. Locate the current chat with this text: "paste a unique sentence from this chat", then dry-run restoring old conversations into the current account framework.
```

The skill should dry-run first. Apply only after reviewing the planned changes and backup path.

## Manual Usage

Dry-run with a known current thread id:

```bash
python3 ~/.codex/skills/codex-history-provider-restore/scripts/restore_history_provider.py \
  --current-thread-id <thread-id>
```

Dry-run by searching for unique current-chat text:

```bash
python3 ~/.codex/skills/codex-history-provider-restore/scripts/restore_history_provider.py \
  --search-text "paste a unique sentence from the current chat"
```

Apply after confirming the plan:

```bash
python3 ~/.codex/skills/codex-history-provider-restore/scripts/restore_history_provider.py \
  --search-text "paste a unique sentence from the current chat" \
  --apply
```

## Safety Notes

- Run dry-run first.
- Keep the generated backup directory.
- Close or restart Codex after applying if the sidebar does not refresh.
- Do not use this on a shared machine without confirming which `~/.codex` profile is being modified.

## License

Choose a license before publishing. MIT is a common default for small utility skills.
