# hAI.Krawl

[![Buy me a coffee](https://img.shields.io/badge/Buy%20Me%20A%20Coffee-ffdd00?style=for-the-badge&logo=buy-me-a-coffee&logoColor=black)](https://www.buymeacoffee.com/highfish)

[![GitHub Stars](https://img.shields.io/github/stars/jbkunama1/hAI.Krawl?style=flat-square&logo=github)](https://github.com/jbkunama1/hAI.Krawl/stargazers)
[![GitHub Forks](https://img.shields.io/github/forks/jbkunama1/hAI.Krawl?style=flat-square&logo=github)](https://github.com/jbkunama1/hAI.Krawl/network/members)
[![GitHub Issues](https://img.shields.io/github/issues/jbkunama1/hAI.Krawl?style=flat-square)](https://github.com/jbkunama1/hAI.Krawl/issues)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=flat-square)](LICENSE)
[![Docker](https://img.shields.io/badge/Docker-ready-2496ED?style=flat-square&logo=docker)](docker-compose.yml)
[![Upstream](https://img.shields.io/badge/Upstream-BlessedRebuS%2FKrawl-orange?style=flat-square&logo=github)](https://github.com/BlessedRebuS/Krawl)

> **Portainer Stack** für [Krawl](https://github.com/BlessedRebuS/Krawl) – Honeypot-Deception-Server als einzelner Docker-Container. Deploy direkt aus GitHub in Portainer, kein manuelles Clonen nötig.

---

## ℹ️ Beschreibung

[Krawl](https://github.com/BlessedRebuS/Krawl) ist ein Cloud-nativer Deception-Server, der bösartige Angreifer, Web-Crawler und automatisierte Scanner erkennt, verlangsamt und analysiert. Er erzeugt realistische Fake-Webanwendungen mit scheinbar verlockenden Zielen (Admin-Panels, Config-Dateien, Fake-Zugangsdaten), um verdächtige Aktivität anzuziehen und zu identifizieren.

Dieses Repo liefert einen fertigen **Portainer Stack** (Git-Deployment) für beide Varianten:

1. **Host-Build** – baut das Image beim Deploy direkt aus dem Upstream-Repo (Standard).
2. **GHCR-Image** – ein GitHub Actions Workflow baut das Image bei jedem Push auf `main` und pusht es auf `ghcr.io`; Portainer holt nur noch das fertige Image.

Läuft im **Standalone-Modus** (SQLite + In-Memory-Cache) – ein einzelner Container, keine externen Dienste nötig.

---

## 🚀 Portainer Deploy – Host-Build (Standard, unverändert)

### Voraussetzungen
- Portainer Business oder CE ≥ 2.x
- Zugriff auf das Internet vom Docker-Host aus

### Schritte

1. In Portainer → **Stacks** → **+ Add stack**
2. Name: `krawl`
3. Build method: **Repository**
4. Repository URL: `https://github.com/jbkunama1/hAI.Krawl`
5. Repository reference: `refs/heads/main`
6. Compose path: `docker-compose.yml`
7. **Environment variables** setzen (siehe unten)
8. **Deploy the stack**

---

## 📦 Portainer Deploy – GHCR-Image (vorgebaut)

Das Image wird von GitHub Actions gebaut (`.github/workflows/docker-build.yml`, bei jedem Push/Commit) und auf `ghcr.io/jbkunama1/hai.krawl` gepusht; ein täglicher Upstream-Check (`docker-build-daily.yml`) baut bei neuen Commits automatisch nach (multi-arch `linux/amd64` + `linux/arm64`).

### Voraussetzungen
- Portainer Business oder CE ≥ 2.x
- **Einmalig:** GHCR-Paket als **public** setzen (GitHub → Repo → **Packages** → `hai.krawl` → **Package settings** → **Change visibility** → Public), sonst braucht Portainer Login-Credentials.

### Schritte

1. In Portainer → **Stacks** → **+ Add stack**
2. Name: `krawl`
3. Build method: **Repository**
4. Repository URL: `https://github.com/jbkunama1/hAI.Krawl`
5. Repository reference: `refs/heads/main`
6. Compose path: `docker-compose.ghcr.yml`
7. **Environment variables** setzen (siehe unten)
8. **Deploy the stack** – Portainer pullt das vorgebaute Image (`pull_policy: always`), kein Build auf dem Host.

---

## 🔧 Environment Variables

| Variable | Beschreibung | Beispiel |
|---|---|---|
| `KRAWL_DASHBOARD_SECRET_PATH` | Dashboard-Pfad (muss mit `/` beginnen). **Zufälligen Wert setzen**, damit das Dashboard nicht erratbar ist. Default: `/home/KRAWL` | `/home/KRAWL` |
| `KRAWL_DASHBOARD_PASSWORD` | Passwort für geschützte Panels. Nur setzen, wenn ein festes Passwort gewünscht ist. Nicht setzen = Krawl generiert eines (steht in den Container-Logs). Nicht leer mitgeben! | `mein-geheimes-passwort` |
| `KRAWL_DELAY` | Antwortverzögerung in ms (Standard 100) | `100` |
| `KRAWL_CANARY_TOKEN_URL` | Canary-Token-URL (optional) | `http://your-canary-token-url` |
| `KRAWL_CUSTOM_TEMPLATE_PATH` | Pfad **im Container** zu einer eigenen HTML-Fake-Seite. Leer lassen = Standard-Seite. Template muss `{counter}` und `{content}` enthalten. | `/templates/custom_page.html` |
| `KRAWL_AI_ENABLED` | KI-generierte Fake-Seiten aktivieren (`true`/`false`). Default: `false` | `true` |
| `KRAWL_AI_PROVIDER` | KI-Provider: `openrouter` oder `openai`. Default: `openrouter` | `openrouter` |
| `KRAWL_AI_OPENAI_BASE_URL` | Basis-URL für Provider `openai`. Nur relevant für eigene/self-hosted Endpoints (z.B. Ollama/llama.cpp) | `http://krawl-llm:8080/v1` |
| `KRAWL_AI_API_KEY` | API-Key für OpenRouter oder OpenAI | `sk-...` |
| `KRAWL_AI_MODEL` | KI-Modell. Free-Modelle bei OpenRouter möglich | `nvidia/nemotron-3-super-120b-a12b:free` |
| `KRAWL_AI_TIMEOUT` | Timeout für API-Aufrufe in Sekunden. Default: `300` | `300` |
| `KRAWL_AI_MAX_DAILY_REQUESTS` | Max. KI-Aufrufe pro Tag (Kostenbremse). `0` = unbegrenzt. Default: `100` | `100` |
| `KRAWL_AI_REASONING_ENABLED` | Reasoning für OpenRouter-Modelle aktivieren. Default: `false` | `false` |
| `KRAWL_AI_REASONING_EFFORT` | Reasoning-Stufe: `none`, `minimal`, `low`, `medium`, `high`, `xhigh` | `medium` |
| `KRAWL_LOG_LEVEL` | Log-Level | `INFO` |
| `PORT` | Host-Port (Container-Port ist 5000) | `5000` |
| `TZ` | Zeitzone | `Europe/Berlin` |

Falls du eine `.env`-Datei nutzen möchtest: Kopiere `.env.example` und fülle die Werte aus.

### 🎨 Eigene Fake-Seite (Custom Template)

Ein Beispiel-Template liegt im Repo unter [`templates/custom_page.html`](templates/custom_page.html) (sieht aus wie eine nginx-Standardseite). So aktivierst du es:

1. Auf dem Docker-Host einen Ordner `templates` anlegen (bzw. den Default `/templates` nutzen) und `custom_page.html` aus dem Repo hineinkopieren. Das Template muss die Platzhalter `{counter}` und `{content}` enthalten. Der Mount ist in der Compose bereits aktiv:
   ```yaml
   - ${KRAWL_TEMPLATES_HOST_DIR:-/templates}:/templates:ro
   ```
2. Die Env-Variable `KRAWL_CUSTOM_TEMPLATE_PATH` auf den Pfad im Container setzen, z.B. `/templates/custom_page.html`. Leer lassen = Standard-Seite.

---

## 🤖 KI-generierte Fake-Seiten (AI-Generated Deception Pages)

Krawl kann auf **unbekannte Pfade** per LLM realistische Fake-Seiten erzeugen (statt nur statische Templates zu liefern). Generierte Seiten werden in der SQLite-DB gecacht (Dashboard → Tab **Deception**) und gegen das tägliche Limit gezählt. Das Feature ist **standardmäßig aus** – so aktivierst du es:

### Option 1: OpenRouter (einfach, kostenlose Modelle)

1. Kostenloses Konto auf [openrouter.ai](https://openrouter.ai) anlegen und API-Key erstellen.
2. Stack-Variablen setzen:

```
KRAWL_AI_ENABLED=true
KRAWL_AI_PROVIDER=openrouter
KRAWL_AI_API_KEY=<dein-OpenRouter-Key>
KRAWL_AI_MODEL=nvidia/nemotron-3-super-120b-a12b:free   # free = kein Guthaben nötig
KRAWL_AI_MAX_DAILY_REQUESTS=100                          # Kostenbremse, z.B. 5–10
```

> Die Vars `OPENROUTER_API_KEY` und `OPENROUTER_MODEL` werden ebenfalls direkt unterstützt und haben Vorrang.

### Option 2: Self-hosted LLM (Ollama / llama.cpp, OpenAI-kompatibel)

Eigenen LLM-Container betreiben (z.B. im selben Netz `highfishNetwork`) und den OpenAI-kompatiblen Endpoint angeben:

```
KRAWL_AI_ENABLED=true
KRAWL_AI_PROVIDER=openai
KRAWL_AI_OPENAI_BASE_URL=http://krawl-llm:8080/v1
KRAWL_AI_API_KEY=<key-des-LLM>      # manche self-hosted Server ignorieren den Key
KRAWL_AI_MODEL=<dein-modell>
```

### Option 3: OpenAI API

```
KRAWL_AI_ENABLED=true
KRAWL_AI_PROVIDER=openai
KRAWL_AI_API_KEY=<openai-key>
KRAWL_AI_MODEL=gpt-5.1-mini
```

**Hinweise**

- `KRAWL_AI_MAX_DAILY_REQUESTS` ist die Kostenbremse. `0` = unbegrenzt. Die offiziellen Kosten liegen bei ~$0.001/Seite (OpenRouter, Standard-Modelle) – mit Free-Modellen oder self-hosted entstehen keine Kosten.
- `KRAWL_AI_REASONING_ENABLED=true` funktioniert nur mit OpenRouter-Modellen, die Reasoning unterstützen.
- Alle Variablen sind optional – einfach weglassen oder leer lassen, dann gelten die (passenden) Defaults aus dem Image.

---

## 📂 Dateistruktur

```
hAI.Krawl/
├── docker-compose.yml        ← Portainer Stack (baut Image aus Upstream-Repo)
├── docker-compose.ghcr.yml   ← Portainer Stack (holt vorgebauten GHCR-Image)
├── .env.example              ← Vorlage für Umgebungsvariablen
├── templates/
│   └── custom_page.html      ← Beispiel-Fake-Seite (nginx-Stil)
├── .github/workflows/
│   ├── docker-build.yml          ← baut & pusht Image auf ghcr.io (bei Push/Commit)
│   ├── docker-build-daily.yml    ← täglicher Upstream-Check; baut neu bei neuem Upstream-Commit
│   └── trufflehog.yml            ← täglicher Secret-Scan
└── README.md
```

---

## 📝 Upstream

Dieses Repo deployt direkt den Quellcode von [BlessedRebuS/Krawl](https://github.com/BlessedRebuS/Krawl).
Das Image wird beim ersten Deploy vom Docker-Host gebaut (kein vorgefertigtes Image nötig) – oder alternativ automatisch vom GHCR-Workflow gebaut und auf `ghcr.io/jbkunama1/hai.krawl` gepusht.

Ein **täglicher Workflow** (`docker-build-daily.yml`, täglich 03:30 Uhr UTC + manuell auslösbar) prüft, ob im Upstream ein neuer Commit vorliegt. Ist das Image dafür noch nicht gebaut, läuft ein Neu-Build und Pusht auf `latest` (+ `upstream-<sha>`-Tag).

---

## 📄 Lizenz

MIT – entsprechend dem Upstream-Projekt.
