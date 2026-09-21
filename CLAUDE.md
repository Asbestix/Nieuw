@AGENTS.md

## Extra voor Claude Code

### Wat er in de agent-container zit (getest 20-09-2026)

- Python 3.11 met pip, Node 22, curl via een proxy.
- Godot 4.5 headless is te downloaden van GitHub Releases en werkt met
  `--headless --import` en `--script`.
- Grote downloads en tijdelijke projecten horen in de **scratchpad-map van de
  sessie**, nooit in de repo.
- Let op: de CI van Jarvis draait op Python 3.12 en `requirements-ci.txt`
  installeert niet op 3.11. Installeer losse pakketten als je een specifieke
  test lokaal wil draaien, en zeg er in je PR bij dat je omgeving afwijkt van
  CI.

### PR-lus

`git push -u origin <branch>`, dan een **draft-PR met label `claude`**, dan
bewaken tot CI groen is, dan **zelf samenvoegen** (squash, PR-nummer in de
titel) — besluit B2. Schuift `main` intussen en past de PR niet meer schoon,
dan eerst `main` erin mergen en CI opnieuw laten lopen. Na het samenvoegen je
regel uit `docs/AAN_HET_WERK.md` halen en het werkbord bijwerken.

### Meldingen naar het toestel van de eigenaar

Gebruik Jarvis' bridge-verb **`notify_send`**. Níét `termux-notification` en
níét `shortcuts/jarvis_notify.sh`.

Gemeten: Jarvis draait op de Fold in een eigen omgeving onder `/app`, en Termux
ziet die bestanden niet — een scriptaanroep werkt daar dus niet. De bridge wel,
die spreekt HTTP:

```
POST $BRIDGE_URL/api/bridge/notify_send
header X-Bridge-Token
body   {"titel", "tekst", "id", "prio", "actie"}
```

`actie` is begrensd tot exact `termux-open '<absoluut pad>'`.

De schakelaar `bridge_notify_send_enabled` staat **standaard uit** en is bewust
**niet zelf-armbaar**: alleen de eigenaar zet hem aan, op het toestel. Vraag het
hem; bouw er geen omweg omheen.

### Geheimen

Nooit in de repo — ook niet in een testfixture, een commentaarregel of een
PR-body. Ze horen in `~/.config/nieuw/` op het toestel, of als repo-secret.
Toon nooit `${VAR:-…}` om te zien of een geheim gezet is: bij een **gezette**
variabele geeft `:-` de waarde terug. Gebruik `${VAR:+ja}`, of een
fingerprint: `printf '%s' "$VAR" | sha256sum | cut -c1-8`.
