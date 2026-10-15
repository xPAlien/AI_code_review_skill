# AI Code Review Skill (moved)

This skill now lives in the Pre-Ship Checklist collection:

https://github.com/xPAlien/pre_ship_checklist_skill/tree/master/skills/ai-code-review

This repository is archived. The skill files were removed from this repository so new installs do not pick up the outdated version. Git history keeps the original files.

## Install the current version

```sh
npx skills@1.7.2 add xPAlien/pre_ship_checklist_skill --global --agent claude-code codex --skill ai-code-review --copy
```

For one project, run the command from its root without `--global`.

## What changed in the move

- Retries apply only to idempotent operations or requests carrying an idempotency key (CR-2.3). The old rule retried every network call, which can duplicate payments and other writes.
- CORS guidance follows the endpoint audience. A wildcard origin fits only public, non-credentialed responses.
- Thresholds for debounce, timeouts, and rate limits are starting points, not universal rules.
- New `NEEDS VERIFICATION` status, a read-only default, stable `CR-x.y` item IDs, and a severity mapping to the collection's `ship` release gate.
- The checklist still has 45 items with the same numbering.

## If you installed the old copy

Back up `~/.claude/skills/ai-code-review` or `.claude/skills/ai-code-review`, move it out of the skills folder, then install the current version with the command above. The collection's migration guide covers backups and ownership: https://github.com/xPAlien/pre_ship_checklist_skill/blob/master/docs/skills-cli-migration.md

## License

MIT License. See [LICENSE](LICENSE).
