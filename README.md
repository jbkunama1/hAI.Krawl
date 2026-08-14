# hAI.Krawl

[![Buy me a coffee](https://img.shields.io/badge/Buy%20Me%20A%20Coffee-ffdd00?style=for-the-badge&logo=buy-me-a-coffee&logoColor=black)](https://www.buymeacoffee.com/highfish)

[![GitHub Stars](https://img.shields.io/github/stars/jbkunama1/hAI.Krawl?style=flat-square&logo=github)](https://github.com/jbkunama1/hAI.Krawl/stargazers)
[![GitHub Forks](https://img.shields.io/github/forks/jbkunama1/hAI.Krawl?style=flat-square&logo=github)](https://github.com/jbkunama1/hAI.Krawl/network/members)
[![GitHub Issues](https://img.shields.io/github/issues/jbkunama1/hAI.Krawl?style=flat-square)](https://github.com/jbkunama1/hAI.Krawl/issues)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=flat-square)](LICENSE)
[![Docker](https://img.shields.io/badge/Docker-ready-2496ED?style=flat-square&logo=docker)](docker-compose.portainer.yml)
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
6. Compose path: `docker-compose.portainer.yml`
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
| `KRAWL_DASHBOARD_SECRET_PATH` | Dashboard-Pfad (muss mit `/` beginnen) | `/dashboard` |
| `KRAWL_DASHBOARD_PASSWORD` | Passwort für geschützte Panels. Nur setzen, wenn ein festes Passwort gewünscht ist. Nicht setzen = Krawl generiert eines (steht in den Container-Logs). Nicht leer mitgeben! | `mein-geheimes-passwort` |
| `KRAWL_LOG_LEVEL` | Log-Level | `INFO` |
| `PORT` | Host-Port (Container-Port ist 5000) | `5000` |
| `TZ` | Zeitzone | `Europe/Berlin` |

Falls du eine `.env`-Datei nutzen möchtest: Kopiere `.env.example` und fülle die Werte aus.

---

## 📂 Dateistruktur

```
hAI.Krawl/
├── docker-compose.portainer.yml  ← Portainer Stack (baut Image aus Upstream-Repo)
├── docker-compose.ghcr.yml       ← Portainer Stack (holt vorgebauten GHCR-Image)
├── .env.example                  ← Vorlage für Umgebungsvariablen
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