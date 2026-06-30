# Bureau Michel : l'infrastructure

Le Bureau Michel est le bureau technique de LEO. Porté par **Léo Copilote** (profil `leo-copilot`), il gère tout ce qui touche au fonctionnement de l'infrastructure : crons, dashboards, n8n, Google APIs, Git, budget, serveur, sécurité.

C'est le padron de la machine — il a accès root complet (`sudo` sans restriction).

## Son rôle

```
Bureau Michel = l'ingénieur système de LEO
├── 🔧 30 crons automatisés
├── 📊 8 dashboards temps réel
├── 🔄 6 workflows n8n
├── 🌐 Nginx + Cloudflare Tunnel
├── 🔒 UFW + SSL + DNS
├── 💰 Suivi du budget DeepSeek
├── 🖥️ Monitoring 3 machines
└── 🔑 Accès root complet (sudo)
```

## Les 8 experts du bureau

| Expert | Compétence | Activer quand... |
|:-------|:-----------|:-----------------|
| 🔧 **SysAdmin** | Administration serveur (Nginx, UFW, Docker) | Installation service, config système |
| 🐳 **DevOps** | Déploiement conteneurs, CI/CD | Nouveau déploiement, mise à jour |
| 📜 **Scripteur** | Scripts Python/Bash, automation | Création de script, correction bug |
| 📊 **DataDoc** | Documentation, rapports, archives | Archivage analyse, production doc |
| 🌐 **Networker** | Cloudflare, DNS, ports, tunnels | Configuration réseau, problème DNS |
| 📈 **DashBuilder** | Dashboards Chart.js, GitHub Pages | Création dashboard, debug HTML |
| ⏰ **CronMaster** | Crons Hermes, staggering, scheduling | Création cron, erreur récurrente |
| 🛡️ **GitGuardian** | Git, sync Drive↔GitHub, clean trees | Dirty files, sync cross-repo |

## Infrastructure gérée

### Serveur LEO

```yaml
OS: Ubuntu 26.04 (resolute)
Kernel: 7.0.0
CPU: i7-7700K
RAM: 22.94 Go
GPU: NVIDIA RTX 3050 (CUDA 13.2)
SSD: 465 Go (/dev/sda2)
HDD: 1 To (/dev/sdb2 → /mnt/data)
```

### Conteneurs Docker

| Conteneur | Image | Rôle | Port |
|:----------|:------|:-----|:----:|
| `hermes-agent` | nousresearch/hermes-agent | Agent IA principal | — |
| `n8n` | n8nio/n8n | Automatisation workflows | 5678 |
| `ollama` | ollama/ollama | LLM local (qwen2.5:7b) | 11434 |
| *(code-server)* | code-server | VS Code web | 8081 |

Le conteneur Hermes a des **pleins pouvoirs** :
- Socket Docker monté en RW (`/var/run/docker.sock`)
- Filesystem hôte monté en RW (`/host`)
- Mode réseau `--network host` (accès direct à tous les ports)

### Nginx + Cloudflare

```
Utilisateur ──→ tofdan.be ──→ Cloudflare ──→ Tunnel ──→ Nginx (port 80)
                                                                  │
                                                                  ▼
                                                            /var/www/
                                                            ├── tofdan.be/
                                                            └── astro/
```

- **Nginx** : sert les sites statiques sur le port 80 seulement
- **Cloudflare** : gère le HTTPS (mode Flexible), le tunnel et le DNS
- **UFW** : ports ouverts 80, 443, 11434, 3389, 7844

### Machines surveillées

| Machine | OS | RAM | Surveillance |
|:--------|:---|:---:|:------------|
| **LEO** | Ubuntu 26.04 | 23 Go | CPU, RAM, disque (21% utilisé) |
| **Yoga** | Windows 11 | — | CPU, RAM (via SSH) |
| **Penguin** | Debian 13 | 6.3 Go | CPU, RAM, VS Code + Kilo Code |

## Les 30 crons

Les crons sont le cœur de l'automatisation. Environ 30 tâches planifiées tournent 24/7 :

### Crons horaires (métriques + dashboard)

```yaml
- Nom: Budget Check
  Horaire: 08:00, 20:00
  Action: Vérifie solde DeepSeek, alerte si < 10€
  Coût: 0€ (no_agent)

- Nom: Dashboard LEO
  Horaire: Toutes les heures
  Action: Collecte métriques → met à jour dashboard KPI
  Coût: 0€ (no_agent)

- Nom: Dashboard Machines
  Horaire: Toutes les heures
  Action: CPU/RAM/disque des 3 machines
  Coût: 0€ (no_agent)

- Nom: Dashboard Crons
  Horaire: Toutes les heures
  Action: Statut des 30 crons
  Coût: 0€ (no_agent)
```

### Crons quotidiens

```yaml
- Nom: Backup → GDrive
  Horaire: 04:00
  Action: Archive tous les profils + config → Google Drive
  Rétention: 7 jours
  Coût: 0€ (no_agent)

- Nom: Veille IA
  Horaire: 07:00
  Action: Collecte 15 sources RSS → analyse DeepSeek → email
  Coût: ~0,05 €/jour

- Nom: Sync Drive → GitHub
  Horaire: 18:00
  Action: Miroir bidirectionnel Drive ↔ GitHub
  Coût: 0€ (no_agent)

- Nom: Classifieur Gmail
  Horaire: Toutes les 15 min
  Action: Nouveaux emails → classification Ollama (9 labels)
  Coût: 0€ (Ollama local)
```

### Pourquoi "no_agent" ?

La plupart des crons utilisent le mode `no_agent = true`. Cela signifie qu'ils exécutent un script **sans LLM**, ce qui les rend totalement gratuits :

```yaml
# Cron avec LLM : coûte de l'argent à chaque exécution (~0,05 €)
cron-veille:
  no_agent: false  # DeepSeek analyse les articles

# Cron sans LLM : 0 € à chaque exécution
cron-metrics:
  no_agent: true   # Simple script bash/python
  script: collect-metrics.sh
```

Sur 30 crons, **22 sont en no_agent** — le coût total des crons est d'environ **0,10 €/jour**.

## Les 8 dashboards

| Dashboard | URL | Contenu |
|:----------|:----|:--------|
| **LEO KPI** | `christophedanhier-hash.github.io/dashboard-leo/` | Sessions, tokens, budget |
| **BAVI LEO** | `christophedanhier-hash.github.io/bavi-leo-dashboard/` | KPIs voyages |
| **Machines** | `christophedanhier-hash.github.io/leo-metrics/` | CPU, RAM, disque (3 machines) |
| **Crons** | `christophedanhier-hash.github.io/crons-dashboard/` | Statut 30 crons |
| **GitHub** | `christophedanhier-hash.github.io/github-dashboard/` | Activité repos |
| **n8n** | `christophedanhier-hash.github.io/dashboard-n8n/` | Workflows, exécutions |
| **Global** | `christophedanhier-hash.github.io/leo-global-dashboard/` | Portail agrégé |
| **Nest** | Dashboard domotique | Capteurs maison |

Tous ces dashboards sont des fichiers **HTML statiques** (zéro backend) :
1. Un script collecte les données → JSON
2. Un template Chart.js génère le HTML
3. Push sur GitHub Pages → site en ligne

## Budget DeepSeek

Le Bureau Michel suit le budget en temps réel :

| Métrique | Valeur |
|:---------|:------:|
| Solde | **$60.31** |
| Dépense totale | $0.41 |
| Moyenne quotidienne | $0.03 |
| Jours restants | **2 315** (>6 ans) |

Le secret de ce coût ridicule : **Ollama local pour la classification**, **DeepSeek Flash pour le quotidien**, **DeepSeek Pro seulement pour les analyses complexes**.

## n8n — Workflows d'automatisation

n8n est utilisé pour les workflows qui nécessitent des webhooks ou des intégrations API :

- **6 workflows actifs**, ~493 exécutions
- **4 credentials** (Google, GitHub, etc.)
- Accès via Tailscale uniquement (`100.92.102.28:5678`)
- **Base SQLite** dans un volume Docker dédié

Workflow emblématique : **LEO Ping** — un endpoint `GET /webhook/ping` qui renvoie `{"response":"pong"}`. Simple, mais essentiel pour vérifier que le service tourne.

## Auto-heal et watchdogs

Le système ne se contente pas de tourner — il se surveille :

```yaml
Auto-heal (toutes les 30-60 min):
  ✅ Crons: 19/19 OK
  ✅ Ollama: UP (qwen2.5:7b responsive)
  ✅ n8n: UP (healthz 200)
  ✅ Docker: 3/3 conteneurs up
  ✅ Disque: 21% utilisé (345 Go libre)
  ✅ Token LEO Google: OK
  ❌ Token Christophe: à ré-autoriser
```

Les watchdogs surveillent en continu : code-server, n8n, dashboards, tunnels.

## En résumé

| Composant | Quantité | Coût mensuel |
|:----------|:--------:|:------------:|
| Crons | ~30 | ~0,10 €/j |
| Dashboards | 8 | 0 € (GitHub Pages) |
| n8n workflows | 6 | 0 € (self-hosted) |
| Machines surveillées | 3 | 0 € |
| DeepSeek API | Flash + Pro | ~1,50 €/mois |
| **Total** | | **~1,50-5 €/mois** |

## Voir aussi

- **Ch.22** : Dashboards et monitoring (Partie V)
- **Ch.26** : Tâches planifiées — crons (Partie VI)
- **Annexe B** : Guide de démarrage rapide
