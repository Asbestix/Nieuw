# Werkverdeling: eigenaar, ChatGPT (Codex) en Claude Code

> Status: **voorgesteld**, 20-09-2026. Overgenomen uit `Asbestix/BAD`
> `docs/WERKVERDELING.md` en aangepast aan een verse repo. Wordt *besloten*
> zodra de eigenaar de scope van dit project vastlegt (B1).

## 1. Uitgangspunt

De verdeling gaat niet over "wie kan het", maar over **waar de sterkte ligt**
en **wie welke map bezit**, zodat werk niet dubbel of tegenstrijdig gebeurt.

| | ChatGPT / Codex | Claude Code |
|---|---|---|
| Waar het werkt | In gesprek met de eigenaar, ook op de telefoon en met stem; met Imagegen, browserbediening en DSM (NAS) | In een container met de repo uitgecheckt: Bash, Python, Node, Git, GitHub-PR's, sub-agents, CI-bewaking |
| Sterkste bijdrage | Beelden, ontwerpgesprek, onderzoek met bronnen, bediening van de systemen van de eigenaar | Uitvoeren en bewijzen: code, tests, CI, PR-beheer, samenhang over de hele repo |

Wat Claude **niet** kan: beelden maken, de NAS of DSM bedienen, op een toestel
of bril testen, store-accounts beheren, en vanuit deze sessie geen nieuwe
GitHub-repo aanmaken.

## 2. Wat ChatGPT beter doet

1. **Beelden maken** — concept art, iconen, mockups, texturen (Imagegen).
2. **Ontwerpgesprek met de eigenaar** — brainstormen, regels, verhaal, namen,
   teksten; snel en ook onderweg via stem.
3. **Onderzoek en vergelijking met bronnen** — beleid, licenties, diensten,
   documentatie. Per vraag: conclusie in één zin, bron-URL, datum.
4. **Bediening van de systemen van de eigenaar** — DSM Download Station,
   GitHub via de browser, Jarvis/MAX-routes.
5. **Kleine documentwijzigingen via Codex-PR's** als de eigenaar toch al in
   ChatGPT werkt.

## 3. Wat Claude Code beter doet

1. **Werk dat moet draaien en bewezen worden** — alles wordt in de container
   uitgevoerd vóór het gepusht wordt.
2. **Lange autonome taken met veel stappen** — één opdracht, één PR.
3. **Samenhang over de hele repo** — refactors over veel bestanden, docs
   consistent houden met code.
4. **PR-levenscyclus** — openen, CI-fouten zelf herstellen, reviewopmerkingen
   verwerken, bewaken tot groen, en dan zelf samenvoegen (B2).
5. **Parallel werk en review** — sub-agents, code-review en security-review als
   aparte passen.
6. **Orchestratie** — plan en werkbord bijhouden, besluiten voorbereiden met
   gemeten cijfers in plaats van aannames.

## 4. Eigenaarschap per map

| Map / bestand | Eigenaar | Anderen |
|---|---|---|
| `AGENTS.md`, `CLAUDE.md` | Claude | Voorstellen via issue of PR-opmerking |
| `docs/PLAN.md`, `docs/WERKVERDELING.md` | Claude | Voorstellen via issue of PR-opmerking |
| `docs/AAN_HET_WERK.md` | **Van niemand** | Iedereen schrijft zijn eigen regel, en alleen die |
| `docs/ontwerp/`, `docs/onderzoek/`, `assets/concept/` | ChatGPT/Codex | Claude leest en verwerkt in code |
| `.github/`, `tools/`, en de code-mappen die er komen | Claude | Codex-wijzigingen alleen na afstemming in een issue |
| `README.md` | Eigenaar | Aanpassen na afstemming |
| Besluiten, hardwaretests, accounts, geheimen | Eigenaar | — |
| Het samenvoegen van een PR | De agent die hem opende, zodra CI groen is (B2) | De eigenaar leest achteraf en draait terug wat niet deugt |

## 5. Overdrachtsprotocol

1. **De repo is de enige waarheid.** Wat alleen in een chat staat, bestaat niet
   voor de andere assistent. Elke uitkomst die telt gaat naar de repo of naar
   een GitHub-issue.
2. **ChatGPT → repo.** Tekst via een Codex-PR met label `codex`, of geplakt in
   een issue. Beelden als PNG/WebP van maximaal 1 MB; originelen op de NAS met
   het pad in de PR.
3. **Claude → repo.** PR met label `claude`, met tests of meetresultaten in de
   beschrijving.
4. **Taak = issue.** Titel, doel, gewenst resultaat, eigenaar (`codex`,
   `claude`, `eigenaar`), acceptatiecriterium. Geen werk zonder issue behalve
   triviale fixes.
5. **Statuswoorden verplicht:** besloten, voorgesteld, gebouwd, getest,
   gedeployed — en nooit *gebouwd* of *getest* zonder commando én uitvoer.
6. **Nooit twee agents tegelijk in hetzelfde bestand.** Bij twijfel een issue,
   en altijd eerst een regel in `AAN_HET_WERK.md`.
7. **Geen grote bestanden in Git** (zie `.gitignore`); wel bron-URL, checksum
   en conversiestappen.
8. **Geen geheimen** in repo, issues of PR's.
