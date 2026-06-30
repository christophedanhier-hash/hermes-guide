# L'architecture LEO — Exemple concret

LEO est l'assistant personnel de Christophe. Ce document détaille son architecture pour servir d'exemple à ceux qui veulent construire le leur.

## Identité

```
Nom : LEO
Type : Majordome numérique
Hôte : Linux (Debian-like)
Canal principal : Telegram
Profils : 1 seul (default)
```

## Providers LLM

| Provider | Rôle | Coût |
|----------|------|------|
| DeepSeek 🤖 | Principal (Telegram, conversations, tâches complexes) | Payant ($42 de solde) |
| Ollama 🏠 | Local, gratuit (batch, traitement bulk, qwen2.5:7b) | Gratuit (RTX 3050) |
| Gemini ⚡ | Fallback automatique | Gratuit (quota API) |

## Communications

LEO communique uniquement par **Telegram** (pas d'autre canal). L'email est utilisé **en sortie uniquement** (LEO peut envoyer des emails depuis `leodanhieria@gmail.com`, mais ne reçoit pas de consignes par email).

## Tâches quotidiennes

### Crons (26 actifs, 0$ LLM)

| Cron | Horaire | Type | Coût | Description |
|------|---------|------|------|-------------|
| `🌍 Global Dashboard` | **H:05** | 🔧 Script | **0$** | Portail unique monitoring consolidé |
| `machines-kpi` | **H:00** | 🔧 Script | **0$** | Collecte CPU/RAM/disque 3 machines |
| `budget-check-v6` | **H:05** | 🔧 Script | **0$** | Relevé solde DeepSeek + projection |
| `dashboard-leo` | **H:10** | 🔧 Script | **0$** | Dashboard KPI LEO (sessions, budget) |
| `leo-metrics` | **H:15** | 🔧 Script | **0$** | Dashboard 3 machines |
| `crons-dashboard` | **H:20** | 🔧 Script | **0$** | Monitoring de tous les crons |
| `github-dashboard` | **H:25** | 🔧 Script | **0$** | Activité GitHub (repos Hermes vs Dev) |
| `wiki-sync` | **H:30** | 🔧 Script | **0$** | Synchronisation sources → Wiki MkDocs |
| `bavi-leo-dashboard` | H:05 | 🔧 Script | **0$** | Dashboard KPIs BAVI LEO |
| `dashboard-n8n` | */15 | 🔧 Script | **0$** | Dashboard monitoring n8n |
| `n8n-healthcheck` | */15 | 🔧 Script | **0$** | Ping n8n API |
| `dashboard-watch` | 30 */2 | 🔧 Script | **0$** | Surveillance dashboards + budget ✅ |
| `daily-backup` | 06:00 | 🔧 Script | **0$** | Backup fichiers critiques |
| `drive-sync` | 18:00 | 🔧 Script | **0$** | Sync Drive ↔ GitHub |
| `credentials-check` | Lun 09:00 | 🔧 Script | **0$** | Vérification tokens OAuth |
| `doc-watch-auto` | 00/06/12/18 | 🔧 Script | **0$** | Surveillance docs 5 wikis |
| `Classifieur emails` | 30m | 🧠 Ollama | **0$** 🏠 | Classification Gmail |
| Veille IA (phase 1) | 07:30 | 🔧 Script | **0$** | Collecte RSS 11 sources |
| Veille IA (phase 2) | 08:00 | 🤖 DeepSeek | ~0.05$ | Analyse + email Cowork |
| `check-hermes-update` | 09:00 | 🔧 Script | **0$** | Vérification nouvelle version Hermes |
| `🛡️ Auto-Heal` | **H:45** | 🧠 Agent | **0$** | Détection + correction auto des erreurs |
| `watchdog-code-server` | **\*/5** | 🔧 Script | **0$** | Relance code-server si arrêté |
| `watchdog-code-server-tunnel` | **\*/5** | 🔧 Script | **0$** | Maintient le tunnel SSH code-server |

**>95% des crons sont en no_agent ou Ollama local** (zéro DeepSeek consommé par les tâches planifiées).

### Workflows n8n (redondance)

Depuis juin 2026, certains crons critiques sont **doublés dans n8n** pour bénéficier du retry natif :

| Workflow n8n | Horaire | Rôle | Redondance |
|-------------|---------|------|------------|
| `💰 Budget Check` | H:05 | Appel DeepSeek API → webhook → budget.json | ⚡ Retry 3x + backup Hermes |
| `🛡️ Dashboard Watch v2` | 30min | Ping 6 dashboards HTTP | ⚡ Retry 3x + backup Hermes (2h) |

**Pattern :** n8n = exécution garantie (retry) / Hermes = backup si n8n down. Double filet.

### Dashboards (7)

| Dashboard | Technologie | Màj | Lien |
|-----------|-------------|-----|------|
| 🌍 Global LEO | HTML + CSS | H:05 | [leo-global-dashboard](https://christophedanhier-hash.github.io/leo-global-dashboard/) |
| LEO KPI (budget DeepSeek, sessions) | HTML + Chart.js | H:10 | [dashboard-leo](https://christophedanhier-hash.github.io/dashboard-leo/) |
| BAVI LEO (KPIs session BAVI) | HTML + Chart.js | H:05 | [bavi-leo-dashboard](https://christophedanhier-hash.github.io/bavi-leo-dashboard/) |
| 3 Machines (CPU/RAM/disque) | HTML + CSS | H:15 | [leo-metrics](https://christophedanhier-hash.github.io/leo-metrics/) |
| Crons (22 crons, historique 7j) | HTML + CSS pur | H:20 | [crons-dashboard](https://christophedanhier-hash.github.io/crons-dashboard/) |
| GitHub (22 repos) | HTML + CSS | H:25 | [github-dashboard](https://christophedanhier-hash.github.io/github-dashboard/) |
| n8n (workflows, exécutions) | HTML + CSS | */15 | [dashboard-n8n](https://christophedanhier-hash.github.io/dashboard-n8n/) |

Tous les scripts de déploiement incluent :
- `--allow-empty` + `run_id` dans le footer pour éviter "nothing to commit"
- Force-push fallback si le push est rejeté
- **Rebuild GH Pages API** après push (CDN forcé)
- Validation des clés `budget.json` au déploiement

## Règles de fonctionnement

### 1. Règle #1 : Réfléchir avant d'agir

Avant chaque action impliquant un choix technique, identifier 2-3 approches, peser le pour/contre, choisir la meilleure. La précipitation est la première cause d'erreur.

### 2. Arbitrage LLM (3 niveaux)

```
Tâche → Script pur ? → no_agent (0 token)
       → A besoin d'un LLM ? → Ollama (gratuit)
                              → Gemini (fallback)
                              → DeepSeek (payant, premium)
       → Jamais sacrifier la qualité pour économiser
```

### 3. Anti-régression

- **1 envoi max** — email ou action : UNE SEULE tentative
- **Pas de réessai** après échec sans accord explicite
- **Corrections → skills** (pas en mémoire passagère)
- **Zéro répétition** — réponse concise, pas de blabla

### 4. Un seul profil

LEO a vécu la perte d'accès Telegram lors d'un basculement de profil. Leçon apprise : **un seul profil, un seul gateway, tout dedans**.

### 5. Sécurité email

- Envoi UNIQUEMENT depuis `leodanhieria@gmail.com`
- JAMAIS depuis `christophe.danhier@gmail.com`
- Christophe TOUJOURS en CC
- Si erreur : STOP net, ne pas réessayer

## Structure des fichiers

```
/opt/data/
├── config.yaml           → Configuration Hermes
├── .env                  → Variables d'environnement (clés API)
├── google_token.json     → Token OAuth Google
├── hermes-backup.py      → Script de backup
├── deploy_dashboard.py   → Script déploiement dashboard KPI
├── deploy_machines.py    → Script déploiement métriques machines
├── update_budget_v6.py   → Script relevé budget DeepSeek
├── update_machines_kpi.py→ Script collecte métriques machines
└── scripts/
    ├── run-backup.sh
    ├── run-budget.sh
    ├── run-machines-kpi.sh
    ├── run-dashboard.sh
    ├── run-leo-metrics.sh
    ├── run-crons-dashboard.sh
    └── deploy-crons-dashboard.py
```

## Leçons apprises

### 12/06/2026 — Trop de profils

**Problème :** Création d'un profil `local` pour Ollama. Arrêt du gateway `local` = perte d'accès Telegram.

**Solution :** Unifier dans un seul profil, Ollama par API directe. Fiabilité > flexibilité.

### 13/06/2026 — Précipitation

**Problème :** Actions sans réflexion préalable = régressions (mauvais token, erreur OAuth, envoi multiple d'email).

**Solution :** Règle #1 : réfléchir avant d'agir. Toujours.

### 14/06/2026 — Crons instables

**Problème :** Crons qui utilisaient le mauvais Python, scripts introuvables, identité Git manquante, push qui échoue.

**Solution :** Uniformisation : wrappers shell + no_agent + identité Git et token dans le script.

## Inspirez-vous, ne copiez pas

LEO est taillé sur mesure pour Christophe. Votre assistant aura ses propres besoins, règles et personnalité. Prenez ce qui vous est utile, adaptez le reste.
