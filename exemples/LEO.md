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

### Crons (8 actifs, 0$ LLM)

| Cron | Horaire | Type | Coût | Description |
|------|---------|------|------|-------------|
| `machines-kpi` | **H:00** | 🔧 Script | **0$** | Collecte CPU/RAM/disque 3 machines |
| `budget-check-v6` | **H:05** | 🔧 Script | **0$** | Relevé solde DeepSeek + projection |
| `dashboard-deploy` | **H:10** | 🔧 Script | **0$** | Génération et push dashboard Hermes KPI |
| `leo-metrics` | **H:15** | 🔧 Script | **0$** | Génération et push dashboard 3 machines |
| `crons-dashboard` | **H:20** | 🔧 Script | **0$** | Monitoring de tous les crons |
| `github-dashboard` | **H:25** | 🔧 Script | **0$** | Activité GitHub (repos Hermes vs Dev) |
| `daily-backup` | 06:00 | 🔧 Script | **0$** | Backup fichiers critiques vers Google Drive |
| `drive-sync` | 18:00 | 🔧 Script | **0$** | Synchronisation Drive → GitHub |
| `docs-update` | Lun 08:00 | 🧠 Ollama | **0$** 🏠 | Mise à jour docs techniques du T600 |

**100% des crons sont en no_agent ou Ollama local** (zéro DeepSeek consommé par les tâches planifiées).

### Dashboards (6)

| Dashboard | Technologie | Màj | Lien |
|-----------|-------------|-----|------|
| Hermes KPI (budget DeepSeek) | HTML + Chart.js | H:10 | [dashboard-leo](https://christophedanhier-hash.github.io/dashboard-leo/) |
| 3 Machines (CPU/RAM/disque) | HTML + CSS | H:15 | [leo-metrics](https://christophedanhier-hash.github.io/leo-metrics/) |
| Crons LEO (monitoring 8 crons) | HTML + CSS pur | H:20 | [crons-dashboard](https://christophedanhier-hash.github.io/crons-dashboard/) |
| Suivi Budget APIs Léo | Google Sheets | H:00/H:05 | Drive LEO |
| Suivi Machines LEO | Google Sheets | H:00 | Drive LEO |
| Dashboard Guide Hermes | Google Docs | Manuel | Drive LEO → GitHub |

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
