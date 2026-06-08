# PowerShell 7.6+ Skill

This repo publishes one reusable `skills.sh` skill for modern Windows PowerShell guidance:

- `powershell-7-6-plus` in `skills/powershell-7-6-plus/SKILL.md`

## Install from GitHub

```bash
npx skills add priyanshuchawda/powershell-7-6-plus-skill --skill powershell-7-6-plus
```

## Test locally

```bash
npx skills add . --list
npx skills use . --skill powershell-7-6-plus
npx skills add . --skill powershell-7-6-plus -y
```

## Notes

- `skills.sh` discovers skills from repository paths such as `skills/<skill>/SKILL.md`.
- `skills.sh.json` only affects grouping/display on the `skills.sh` repo page.
- Visibility on `skills.sh` depends on the repo being installed or seen by the telemetry service, and page updates can lag behind cache refresh.
