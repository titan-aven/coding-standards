# Coding Standards & Guardrails

Diese Datei ist die verbindliche Grundlage für jede Code-Arbeit im gesamten
Agent-Team. Gilt für neue UND bestehende Repos. Festgelegt gemeinsam von Kai
und Luna (17.10.2026), Grundlage: 3 Meeting-Transkripte (AI in Software Dev,
Automation/ML Workflow, Git/GitHub/VS Code Workshop) + moderne
Software-Engineering-Praxis.

## 0. Code-Hoheit

Code wird AUSSCHLIESSLICH von **Titan** geschrieben, geändert oder committed.
Kein anderer Agent (Luna, Aven, Hera, Lexi, Vera, Atlas, Nova) schreibt
jemals selbst Code oder Skripte — auch nicht "kleine Skripte nebenbei".
Jede Code-Anfrage wird an Titan delegiert. Grund: nur so greifen die
folgenden Guardrails überhaupt — Code an anderer Stelle wäre unkontrollierte
Schatten-IT.

## 1. Branches & Pull Requests

- Kein direkter Push auf `main`. Jede Änderung läuft über einen Feature-Branch.
- Branch → Commits → Pull Request → Review → CI-Checks grün → Merge.
- `main` bleibt dadurch immer in einem funktionierenden Zustand.

## 2. CI/CD-Gates

Jedes Repo (neu und bestehend, rückwirkend für luna-voice, aven-voice,
photo-edit-skill) bekommt automatisierte Pflicht-Checks bei jedem PR:

1. **Linting** — Stil-/Fehler-Scan
2. **Tests** — gegen Mock-/Platzhalterdaten, nie gegen echte Nutzerdaten
3. **Build-Check** — läuft/startet der Code überhaupt

Technisch erzwungen über GitHub **Required Status Checks** in den Branch
Protection Rules — ein PR kann nicht gemerged werden, bevor alle Checks grün
sind. Das gilt auch für Titan selbst, unabhängig von Admin-Rechten.

## 3. Code Review & Ownership

- Jeder Pull Request wird von **Luna** reviewed, bevor er gemerged werden darf.
- Technisch über **CODEOWNERS** + "Require approval from someone other than
  the last pusher" in den Branch Protection Rules abgesichert.
- Luna reviewt inhaltlich gegen genau diese Datei (Architektur, Secrets,
  Tests vorhanden, ADR bei relevanten Entscheidungen).

## 4. Architektur-Prinzipien

- **Separation of Concerns** — jeder Codeteil macht genau eine Sache.
- **Keine Magic Numbers / hardcoded Werte** — benannte, dokumentierte
  Konstanten statt Zahlen/Strings im Code vergraben.
- **Mocks statt echte Daten in Tests** — Tests laufen gegen
  Platzhalterdaten, nie gegen echte Nutzerdaten oder Live-Systeme.
- **Single Source of Truth** — jede Information existiert an genau einer
  Stelle, nicht mehrfach kopiert über Dateien verteilt.

## 5. Architecture Decision Records (ADRs)

Bei jeder wichtigeren technischen Entscheidung (Technologie-Wahl,
Architektur-Umbau, Abkehr von einem Standardansatz) legt Titan
**automatisch** eine kurze ADR-Datei an (`docs/adr/NNNN-titel.md`), die
festhält:
- Was wurde entschieden
- **Warum** (das ist der wichtige Teil — nicht nur das Ergebnis)
- Welche Alternativen wurden verworfen und weshalb

Ziel: ein neuer Entwickler oder Agent kann jederzeit einsteigen und versteht
die Begründung hinter bestehenden Entscheidungen, nicht nur den aktuellen
Code-Zustand.

## 6. Secrets Management

- Secrets (API-Keys, Tokens, Passwörter) stehen **niemals** direkt im Code.
- Immer über Umgebungsvariablen / `.env`-Dateien, die via `.gitignore`
  ausgeschlossen sind.
- Ein **Pre-Commit-Hook** prüft vor jedem Commit automatisch auf mögliche
  Secrets im Diff.
- Bei einem Fund: **Warnung, kein Hard-Block** — Titan/Kai werden gefragt,
  ob es sich wirklich um ein Secret handelt (vermeidet Fehlalarme bei
  harmlosen Beispiel-Keys in Dokumentation).

## 7. Reverse Engineering & IP-Grenzen

- **Immer erlaubt:** reines Beobachten von Verhalten/Funktionalität einer
  fremden Software (Screenshots, UI-Interaktion, Netzwerk-Traffic) und
  eigenständiges Nachbauen der beobachteten Funktionalität.
- **Eng begrenzt erlaubt:** Dekompilieren einzig zum Zweck der
  Interoperabilität mit eigener, unabhängig entwickelter Software.
- **Niemals erlaubt:** Dekompilieren, um ein Produkt zu bauen, das einem
  fremden, urheberrechtlich geschützten Original im Wesentlichen ähnelt
  oder es ersetzt (z.B. "Clone von Produkt X als Ersatz").
- Bei Unsicherheit: Luna oder Kai fragen — der Agent entscheidet das nicht
  allein.
- Werkzeuge wie REA (Reverse Engineer Anything) sind ausschließlich bei
  Titan installiert, nicht bei anderen Agents, und unterliegen denselben
  Grenzen wie oben beschrieben.

## 8. Conventional Commits

Jeder Commit folgt dem Format: `typ: kurze Beschreibung`

| Typ | Bedeutung |
|-----|-----------|
| `feat` | neues Feature |
| `fix` | Bugfix |
| `docs` | nur Dokumentation |
| `refactor` | Code umbauen ohne Verhaltensänderung |
| `test` | Tests hinzufügen/ändern |
| `chore` | Wartung (Dependencies, Config) |

Beispiel: `fix: Kostenberechnung rundet negative Werte falsch`

---

*Dieses Dokument ist versioniert und wird iterativ verfeinert, wenn sich
zeigt, dass eine Regel schlechten Output produziert — Änderungen laufen wie
jede andere Code-Änderung über Pull Request + Review.*
