# Dashboards et monitoring

Visualisez l'activité de votre assistant en temps réel avec des dashboards HTML autonomes, déployés sur GitHub Pages.

## Principe

Un dashboard est un fichier HTML statique (zéro JavaScript serveur, zéro backend) :

```
Script de collecte → JSON + HTML → Push GitHub Pages
                                      ↓
                    https://user.github.io/mon-dashboard/
```

**Avantages :** gratuit (GitHub Pages), accessible partout, aucun serveur à maintenir.

## Les dashboards de LEO

LEO a **6 dashboards** en production, tous rafraîchis toutes les heures :

| Dashboard | Contenu | URL | Cron |
|-----------|---------|-----|------|
| **LEO KPI** | Budget DeepSeek, sessions, coûts | [dashboard-leo](https://christophedanhier-hash.github.io/dashboard-leo/) | H:10 |
| **BAVI LEO** | KPIs BAVI (sessions, tokens, budget) | [bavi-leo-dashboard](https://christophedanhier-hash.github.io/bavi-leo-dashboard/) | H:05 |
| **3 Machines** | CPU, RAM, disque LEO/Yoga/Penguin | [leo-metrics](https://christophedanhier-hash.github.io/leo-metrics/) | H:15 |
| **Crons LEO** | État de tous les crons, historique 7j | [crons-dashboard](https://christophedanhier-hash.github.io/crons-dashboard/) | H:20 |
| **GitHub** | Activité repos Hermes vs Développement | [github-dashboard](https://christophedanhier-hash.github.io/github-dashboard/) | H:25 |
| **n8n** | Monitoring workflows n8n | [dashboard-n8n](https://christophedanhier-hash.github.io/dashboard-n8n/) | */15 |
| **Drive Sync** | Dernière sync Drive ↔ GitHub bidirectionnelle | GitHub | 18:00 |

Tous sont générés par des scripts `no_agent` — **0$ de coût LLM** par mise à jour.

## Architecture technique

Chaque dashboard suit le même pattern :

1. **Un script de collecte** (Python) qui :
   - Récupère les données (API, fichiers, logs)
   - Génère un `index.html` avec Chart.js ou CSS pur

2. **Un cron no_agent** qui exécute le script toutes les 4h

3. **Un dépôt GitHub Pages** qui sert le HTML

### Script type

```python
#!/usr/bin/env python3
import json, subprocess
from pathlib import Path

# 1. Collecter les données
data = collecter_metriques()

# 2. Générer le HTML
html = generer_dashboard(data)

# 3. Écrire dans le repo
repo = Path("/tmp/mon-dashboard")
repo.joinpath("index.html").write_text(html)

# 4. Push sur GitHub
subprocess.run(["git", "-C", str(repo), "add", "."])
subprocess.run(["git", "-C", str(repo), "commit", "-m", "Màj dashboard"])
subprocess.run(["git", "-C", str(repo), "push", "origin", "main"])
```

### Déploiement

```bash
# 1. Créer le repo
gh repo create mon-dashboard --public

# 2. Activer GitHub Pages
echo '{"source":{"branch":"main","path":"/"}}' | \
  gh api repos/user/mon-dashboard/pages --input -

# 3. Cloner en local
git clone https://github.com/user/mon-dashboard.git /tmp/mon-dashboard

# 4. Configurer le cron
hermes cron create \
  --script deploy-dashboard.sh \
  --schedule "0 */4 * * *" \
  --name "mon-dashboard" \
  --no-agent
```

## Pitfalls

### 🔴 Pas de JavaScript si le navigateur flashe

Sur certains appareils (Chromebook, mobile), Chart.js en mode responsive peut causer un rafraîchissement en boucle. Solution : **remplacer Chart.js par un tableau CSS statique**.

```css
/* Au lieu d'un graphique JS : tableau statique */
.hist-table td.ok-cell { color: #22c55e; }
.hist-table td.err-cell { color: #ef4444; }
```

### 🔴 Gérer l'identité Git

Dans l'environnement minimal d'un cron, `git commit` échoue si l'identité n'est pas configurée :

```python
subprocess.run(["git", "config", "user.name", "MonAssistant"])
subprocess.run(["git", "config", "user.email", "assistant@exemple.com"])
```

### 🔴 Gérer l'authentification GitHub

Le cron n'a pas de TTY pour le flow OAuth Git. Passez le token dans l'URL :

```python
import os
tok = os.environ.get("GH_TOKEN")
if tok:
    remote = f"https://user:{tok}@github.com/user/repo.git"
    subprocess.run(["git", "remote", "set-url", "origin", remote])
```

### 🔴 Budget désynchronisé

Si le budget affiché sur un dashboard ne correspond pas au `budget.json`, le cron `dashboard-watch` (voir `crons.md`) déclenche une alerte. Vérifiez que les clés lues par le script de déploiement correspondent exactement à celles du JSON :

```python
# Dans budget.json : "avg_daily", "total_spent" (pas "daily_spend")
# Dans le script : budget.get("avg_daily", 0)  # ✅ correct
```

## Surveillance automatique (dashboard-watch)

Un cron **dashboard-watch** (`scripts/dashboard-watch.py`) tourne toutes les 2h et vérifie :

1. **HTTP 200** — chaque dashboard répond
2. **Âge < 2h** — données fraîches
3. **Budget cohérent** — valeur affichée ≈ `budget.json` (écart max 1$)
4. **Redeploiement auto** — si stale ou 404, le script relance le déploiement
5. **Rebuild GH Pages** — après chaque push, appelle l'API pour forcer le rafraîchissement CDN

```python
# Extrait : rebuild GH Pages après push
subprocess.run(["gh", "api", f"repos/user/{repo}/pages/builds", "-X", "POST"])
```

## Idées de dashboards

- **Usage LLM** — requêtes/jour, tokens consommés, coût estimé
- **Système** — CPU, RAM, disque, uptime de votre serveur
- **Projets** — Suivi d'avancement, tâches complétées
- **Réseau** — Latence, bande passante, statut des services

## Pour aller plus loin

- Voir `03-utilisation/crons.md` pour le déploiement automatisé
- Voir `exemples/LEO.md` pour les dashboards en production
