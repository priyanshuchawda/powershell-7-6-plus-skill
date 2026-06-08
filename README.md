# PowerShell 7 Guidance Skill

This repo publishes one reusable `skills.sh` skill for modern Windows PowerShell guidance:

- `powershell7-guidance` in `skills/powershell7-guidance/SKILL.md`

## Install from GitHub

```bash
npx skills add priyanshuchawda/powershell7-guidance-skill --skill powershell7-guidance
```

## Test locally

```bash
npx skills add . --list
npx skills use . --skill powershell7-guidance
npx skills add . --skill powershell7-guidance -y
```

## Notes

- `skills.sh` discovers skills from repository paths such as `skills/<skill>/SKILL.md`.
- `skills.sh.json` only affects grouping/display on the `skills.sh` repo page.
- Visibility on `skills.sh` depends on the repo being installed or seen by the telemetry service, and page updates can lag behind cache refresh.
