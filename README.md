# Modern PowerShell 7 Skill

This repo publishes one reusable `skills.sh` skill for modern Windows PowerShell guidance:

- `powershell-7-modern` in `skills/powershell-7-modern/SKILL.md`

## Install from GitHub

```bash
npx skills add priyanshuchawda/powershell-7-modern-skill --skill powershell-7-modern
```

## Test locally

```bash
npx skills add . --list
npx skills use . --skill powershell-7-modern
npx skills add . --skill powershell-7-modern -y
```

## Notes

- `skills.sh` discovers skills from repository paths such as `skills/<skill>/SKILL.md`.
- `skills.sh.json` only affects grouping/display on the `skills.sh` repo page.
- Visibility on `skills.sh` depends on the repo being installed or seen by the telemetry service, and page updates can lag behind cache refresh.
