# Instructies voor agents (Claude Code, Codex en andere)

NIEUW is een verse repo. De naam is voorlopig: zoek-en-vervang `NIEUW` /
`CI_RUNNER_NIEUW` zodra de eigenaar de echte naam kiest (besluit B1).

Lees vóór elk werk, in deze volgorde:

1. `docs/AAN_HET_WERK.md` — wie waaraan bezig is, nu. Zet je eigen regel erbij
   vóór je begint en push hem meteen; haal hem weg als je PR is samengevoegd.
2. `docs/PLAN.md` — mijlpalen, besluiten (B1, B2, …), werkbord (T1, T2, …).
3. `docs/WERKVERDELING.md` — eigenaarschap per map, overdrachtsprotocol.

## Regels

- **Taal** van docs, commits, issues en PR's: Nederlands.
- **Statuswoorden:** besloten, voorgesteld, gebouwd, getest, gedeployed. Claim
  niets als *gebouwd* of *getest* zonder bewijs: het commando én de uitvoer.
  Een download is geen gevalideerde import; een draft-PR staat niet op `main`.
- **Eén taak per issue en per PR.** Draft-PR met label `claude` of `codex`.
- **Samenvoegen doet de agent zelf** zodra CI groen is en de PR schoon op
  `main` past: squash, PR-nummer in de titel (besluit B2, overgenomen uit BAD
  B14, 20-09-2026). Niet wachten op de eigenaar; die leest achteraf en draait
  terug wat niet deugt. Daarna je eigen regel uit `docs/AAN_HET_WERK.md` halen
  en het werkbord in `docs/PLAN.md` bijwerken.
- **Geen wachtwoorden, tokens of sessiegegevens in de repo.** Ooit. Die horen
  in `~/.config/nieuw/` op het toestel of in repo-secrets.
- **Geen bestanden groter dan 1 MB in Git.** Brondata blijft op de NAS of in
  releases; wel de bron-URL, de checksum en de conversiestappen in de repo.
- **Respecteer het eigenaarschap per map** uit `docs/WERKVERDELING.md`. Nooit
  twee agents tegelijk in hetzelfde bestand; bij twijfel eerst een issue.
- **Werk na afloop het werkbord bij:** status mét bewijs, output, en het
  eerstvolgende open punt.

## CI — `runs-on` is geen detail

Elke job schrijf je zo, vanaf de eerste commit:

```yaml
runs-on: ${{ vars.CI_RUNNER_NIEUW || 'ubuntu-latest' }}
```

Gemeten 20-09-2026: het Actions-budget van dit account raakte op en toen stond
álles stil. Jobs starten dan niet eens — geen logs, geen stappen, enkel de
annotatie *"The job was not started because an Actions budget is preventing
further use."* Zelfgehoste runners verbruiken geen minuten. Zolang de
repo-variabele leeg is verandert de terugval niets; staat ze op een
runner-label, dan wijkt álles in één tik uit naar de eigen pool.

**Eén uitzondering:** een workflow die de runnerpool zélf beheert houdt
`runs-on: ubuntu-latest` letterlijk. Anders kan een kapotte pool zichzelf niet
repareren — de job die "zet de pool aan" moet zeggen zou dan op die pool
wachten.

## Runners activeren

De route staat in Jarvis, `docs/RUNBOOK_SELF_HOSTED_RUNNERS.md` §7b:

- Dispatchen kan **alleen** via de MCP-tool `actions_run_trigger`
  (`run_workflow`, `ci-runner-provision.yml`, `ref: main`).
- `curl` naar de dispatch-API geeft 403, `gh` bestaat niet in de sessie, en
  `/actions/runners` en `/actions/variables` geven 403 vanuit de sandbox. Dat
  zijn **geen storingen**, dat is de bedoelde grens. Omzeil ze niet.
- Lezen (runs, jobs, logs) werkt wél met kale `curl` via de proxy.
- Een dispatch geeft 204 en geen run-id. Vind de run met
  `tools/ci_runner_run_wacht.sh --na <vorig_id>`.
- Volgorde: create → status (wacht op `fase=klaar`) → flip → canary.

⚠️ **Gemeten 20-09-2026 op `origin/main` van Jarvis:** de provisioning-keten is
nog niet repo-onafhankelijk. `tools/ci_runner_provision.py` draagt wél
`--gh-owner` / `--gh-repo` / `--label`, maar `.github/workflows/
ci-runner-provision.yml` geeft die drie **niet** door (geen inputs, en de
`run:`-stappen roepen het script kaal aan), en `flip_variable()` schrijft een
vaste variabelenaam `CI_RUNNER_HEAVY`. Zie T1 in het werkbord.
