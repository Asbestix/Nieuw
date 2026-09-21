# NIEUW — plan

> Beheerd door de orchestrator (Claude Code-sessies) namens de eigenaar.
> Stand: **20 september 2026**. De repo is vandaag opgezet; er is nog geen
> scope en nog geen code. Besluiten neemt de eigenaar; dit document bereidt ze
> voor met gemeten feiten.
>
> Statuswoorden: **besloten · voorgesteld · gebouwd · getest · gedeployed**.
> Nooit *gebouwd* of *getest* zonder het commando én de uitvoer erbij.

Leesvolgorde voor elke nieuwe sessie (mens of agent):
[`AAN_HET_WERK.md`](AAN_HET_WERK.md) → dit plan →
[`WERKVERDELING.md`](WERKVERDELING.md).

## 1. Waar we staan (gemeten, niet gewenst)

| Onderdeel | Stand |
|---|---|
| Repo | Privé, en sinds 21-09-2026 gevuld met de opzet: `AGENTS.md`, `CLAUDE.md`, dit plan, de claimtabel, de werkverdeling, `.gitignore`, `README.md` en één CI-workflow. Geen code, geen licentie. De repo was de eerste vijftien uur onbereikbaar voor de sessie die de opzet bouwde (acht `add_repo`-pogingen); de achtste lukte. |
| Scope | **Nog niet bepaald** — B1 hieronder. Ook de naam is voorlopig: overal `NIEUW`. |
| CI | Drie jobs (hygiëne, docs, code), alle drie op `${{ vars.CI_RUNNER_NIEUW \|\| 'ubuntu-latest' }}`. De code-job is leeg-en-groen tot er code is. |
| Runners | Nog niets aangevraagd. De keten in Jarvis is nog niet repo-onafhankelijk (T1), en de twee repo-secrets zijn nog niet gezet (T2). |
| Meldingen | Route bekend (bridge-verb `notify_send`), schakelaar `bridge_notify_send_enabled` staat uit; alleen de eigenaar kan hem aanzetten (T5). |

## 2. Doel van fase 1

**Nog niet bepaald.** Zodra B1 beslist is, komt hier één alinea die zegt wat
"af" betekent, klein genoeg om in weken te halen.

## 3. Besluiten

Elk besluit heeft een aanbeveling met onderbouwing. Zolang het niet genomen is,
blijft het *voorgesteld*.

### B1 — De naam: **besloten: NIEUW** (eigenaar, 21-09-2026). Wat we bouwen: **open**

**De naam blijft `NIEUW`** — de eigenaar bevestigde dat op 21-09-2026, en
daarmee is de werktitel de echte naam. Gevolg: er valt niets te hernoemen. De
zes plekken (`AGENTS.md`, `CLAUDE.md`, `README.md`, dit plan, `.gitignore` met
`~/.config/nieuw/`, en de CI-variabele `CI_RUNNER_NIEUW` in `ci.yml`) staan al
goed, en de eerste runner-flip kan zonder voorbehoud — dat was de enige
tijdsdruk op dit besluit. **T4 vervalt.**

**Nog wél open: wat het project ís.** Er is één alinea nodig over wat NIEUW
moet worden en wat fase 1 af maakt; zolang die er niet is blijft sectie 2 leeg
en kan er geen code beginnen. Dat blijft T3.

### B2 — Samenvoegen: **besloten: de agent voegt zelf samen** (20-09-2026)

Overgenomen uit BAD-besluit B14 van dezelfde dag: een PR van een agent wordt
samengevoegd door de agent die hem opende, zodra CI groen is en hij schoon op
`main` past — squash, PR-nummer in de titel. De eigenaar leest achteraf en
draait terug wat niet deugt. Wat dat vraagt: CI is het bewijs, dus geen PR
zonder test of meting; wie samenvoegt haalt daarna zijn regel uit
`AAN_HET_WERK.md` en werkt dit werkbord bij; en omdat `main` snel schuift gaat
`main` er eerst nog eens in als de PR niet schoon is.

### B3 — CI op eigen runners: **besloten: variabele met terugval** (20-09-2026)

Elke job draagt `runs-on: ${{ vars.CI_RUNNER_NIEUW || 'ubuntu-latest' }}`,
vanaf de eerste commit. Gemeten 20-09-2026: toen het Actions-budget van dit
account op was, startten jobs niet eens — geen logs, geen stappen, enkel de
annotatie *"The job was not started because an Actions budget is preventing
further use."* Zelfgehoste runners verbruiken geen minuten. Zolang de variabele
leeg is verandert de terugval niets; zetten is één tik, zonder de workflows aan
te raken. Uitzondering: een workflow die de runnerpool zélf beheert houdt
`ubuntu-latest` letterlijk, anders kan een kapotte pool zichzelf niet
repareren.

### B4 — Werkverdeling: **voorgesteld** (20-09-2026)

Zie [`WERKVERDELING.md`](WERKVERDELING.md). Wordt besloten samen met B1, want
de mappenverdeling hangt aan wat het project wordt.

## 4. Mijlpalen

| Mijlpaal | Resultaat | Acceptatie | Vereist |
|---|---|---|---|
| **M0 Fundament** | Leesvolgorde, claimtabel, werkverdeling, `.gitignore`, CI met runner-terugval | Samengevoegd op `main`, CI groen | — |
| **M1 Scope** | Wat het project is en wat fase 1 af maakt (de naam is besloten: NIEUW) | Eén alinea in sectie 2 | M0 |
| **M2 Eerste code** | Het eerste werkende ding, met tests die in CI draaien | CI-job `code` doet echt iets en is groen | M1 |
| **M3 Eigen runners** | CI draait op de eigen pool in plaats van op GitHub-minuten | Een run met `runner_name` uit de eigen pool, en `CI_RUNNER_NIEUW` gezet | M0, T1, T2 |

## 5. Werkbord

Eén taak = één GitHub-issue = één PR. Status altijd mét bewijs.

| # | Taak | Wie | Status | Raakt |
|---|---|---|---|---|
| T0 | Repo opzetten: leesvolgorde, claimtabel, werkverdeling, `.gitignore`, CI met `runs-on`-terugval | Claude | **gebouwd en getest** — YAML geparst met `yaml.safe_load` (3 jobs), en de vier `run:`-blokken lokaal gedraaid in een verse `git init`-checkout: grootte-gate groen, geheimen-gate groen, leesvolgorde-gate groen, claimtabel-gate groen; de negatieve controles (bestand van 2 MB, een `.env`, een weggehaald document, een kapotte tabelkop) falen elk met de bedoelde melding | `AGENTS.md`, `CLAUDE.md`, `docs/`, `.gitignore`, `.github/workflows/ci.yml` |
| T1 | Provisioning repo-onafhankelijk maken in Jarvis: `ci-runner-provision.yml` krijgt inputs voor `--gh-owner`, `--gh-repo` en `--label` en geeft ze door, en `flip_variable()` krijgt de variabelenaam als parameter | Claude (in Jarvis) | **open** — gemeten 20-09-2026 op de checkout van Jarvis: `tools/ci_runner_provision.py` draagt `--gh-owner`/`--gh-repo`/`--label` (regels 41-50), maar de workflow kent die inputs niet en roept het script kaal aan (`create`, `status`, `flip`, … zonder een van de drie), en `flip_variable()` schrijft de vaste naam `CI_RUNNER_HEAVY` (regels 462-467). Zonder deze taak kan de pool alleen Jarvis bedienen | Jarvis: `.github/workflows/ci-runner-provision.yml`, `tools/ci_runner_provision.py` |
| T2 | De twee repo-secrets zetten: `HCLOUD_TOKEN` en `CI_RUNNER_PAT` (fine-grained, **alleen** "Actions: Read and write", bewust géén Contents-rechten) | Eigenaar | **open** — blokkeert M3 | GitHub-secrets van deze repo |
| T3 | B1 beslissen: scope, echte naam, wat fase 1 af maakt | Eigenaar | **open** — blokkeert alles wat code is | sectie 2 en 3 |
| T4 | Naam doorvoeren zodra B1 er is | Claude | **vervallen** 21-09-2026 — B1 koos `NIEUW`, precies de naam die er al overal staat, dus er is niets door te voeren | — |
| T5 | Meldingen naar het toestel: `bridge_notify_send_enabled` aanzetten op de Fold, daarna een proefmelding via `POST $BRIDGE_URL/api/bridge/notify_send` | Eigenaar (schakelaar), Claude (proef) | **open** — de schakelaar staat standaard uit en is bewust niet zelf-armbaar | `CLAUDE.md` |
| T6 | Licentie kiezen en vastleggen | Eigenaar | **open** | `LICENSE`, `README.md` |
| T7 | Deze opzet in de repo krijgen | Eigenaar (app-toegang), Claude (push) | **gedaan** 21-09-2026 — `add_repo` lukte bij de achtste poging, nadat de eigenaar de repo aan de Claude GitHub App had toegevoegd; de set kwam uit de transportmap in Jarvis PR #8345, die daarna gesloten is | hele repo |

## 6. Eerstvolgende stap

**T3 bij de eigenaar**: zeg wat dit project wordt. De naam is beslist (B1:
`NIEUW`), dus dit is het enige dat M2 nog blokkeert. Onafhankelijk daarvan kan
T1 al in Jarvis, en T2 kan de eigenaar op elk moment doen.
