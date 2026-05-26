# Claude Code Instructions

Use this repository as a Markdown instruction pack for Mac cleanup work.

## How To Use

1. Read `SKILL.md`.
2. Follow the activation and mode rules exactly.
3. Read `references/inspection-commands.md` for read-only probes (`df`, `du`, APFS snapshots, sleep image, process probes).
4. Read `references/dev-bucket-paths.md` for the per-ecosystem cleanup catalogue (Xcode, Homebrew, Node, Python, Rust/Go, browsers, heavy native apps, system temp, APFS state).
5. Read `references/maintenance-principles.md` before touching stateful agent data.
6. Read `references/openclaw-cleanup.md` for OpenClaw cleanup requests.
7. Read `references/mole-integration.md` when the user mentions Mole, `mo`, or Mole-assisted cleanup.

## Important Boundaries

- `agents/openai.yaml` is Codex/OpenAI metadata and does not make this a Claude-native skill.
- Do not copy the full skill text into another file. Keep `SKILL.md` as the behavior source.
- Do not run destructive cleanup until the selected mode allows it.
- For ambiguous requests such as `帮我清Mac垃圾`, show the three-option menu from `SKILL.md` first.
- For OpenClaw state, prefer dry-run and backup before changing anything.
- For Mole work, detect `mo` locally, run preview commands first, and require explicit approval for uninstall, purge, optimize, remove, touchid, or completion actions.

## Public Release Gate

Before public GitHub push, tag, release, or asset upload, run:

```bash
python3 "${CODEX_HOME:-$HOME/.codex}/skills/shipgate/scripts/shipgate.py" check . --project-type codex-skill --report-md build/shipgate/report.md --report-json build/shipgate/report.json
```

Stop on any ShipGate `fail` status. Public release README docs must be split pages: `README.md` for English and `README_ZH.md` for Chinese, with reciprocal top links.
