---
name: codex-history-provider-restore
description: Restore Codex conversation history visibility after switching API accounts or providers by relabeling local Codex session JSONL metadata and state_5.sqlite thread rows to the current chat's model_provider. Use when old Codex chats disappear after account switching, provider switching, cc-switch changes, or when the user asks to merge previous Codex conversations into the current chat/account framework without changing original message content.
---

# Codex History Provider Restore

Use this skill to make older Codex Desktop conversations appear under the same local account/provider framework as the current chat. The operation changes only `model_provider` labels in `~/.codex/sessions`, `~/.codex/archived_sessions`, and `~/.codex/state_5.sqlite`; it must not rewrite message bodies.

## Workflow

1. Locate the current chat first. Search `~/.codex` for a unique recent user message, or inspect recent JSONL files under `~/.codex/sessions/YYYY/MM/DD/`.
2. Confirm the current thread id and provider:
   - JSONL path usually ends with `...-<thread-id>.jsonl`.
   - SQLite source of truth is `threads.model_provider` in `~/.codex/state_5.sqlite`.
3. Run the bundled script in dry-run mode. It discovers the target provider from the current thread and reports planned JSONL/SQLite changes.
4. Before applying, tell the user exactly which files will be changed and where backups will be written. If the user already explicitly authorized modifying `~/.codex`, proceed.
5. Run with `--apply`. The script backs up every changed JSONL plus `state_5.sqlite`, then relabels provider fields.
6. Verify there are no planned changes left and report the final provider counts. Tell the user to refresh or restart Codex Desktop if the sidebar does not update immediately.

## Script

Use `scripts/restore_history_provider.py`.

Typical commands:

```bash
python3 ~/.codex/skills/codex-history-provider-restore/scripts/restore_history_provider.py \
  --current-thread-id <thread-id>

python3 ~/.codex/skills/codex-history-provider-restore/scripts/restore_history_provider.py \
  --current-thread-id <thread-id> \
  --apply
```

The default backup location is the caller's `./outputs/codex-history-backups` when available; otherwise pass `--backup-base <path>`.

## Guardrails

- Do not delete files.
- Do not change message bodies or non-provider metadata.
- Preserve archived state, source, title, cwd, timestamps, and rollout paths.
- Use dry-run first unless the user explicitly asks for immediate execution and the current thread id is known.
- If the current chat is already the only provider, stop after verification.
