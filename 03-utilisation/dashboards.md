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

LEO a trois dashboards en production :

| Dashboard | Contenu | URL |
|-----------|---------|-----|
| **Hermes KPI** | Budget DeepSeek, sessions, coûts | `dashboard-leo` |
| **3 Machines** | CPU, RAM, disque LEO/Yoga/Penguin | `leo-metrics` |
| **Crons LEO** | État de tous les crons, historique 7j | `crons-dashboard` |

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

## Idées de dashboards

- **Usage LLM** — requêtes/jour, tokens consommés, coût estimé
- **Système** — CPU, RAM, disque, uptime de votre serveur
- **Projets** — Suivi d'avancement, tâches complétées
- **Réseau** — Latence, bande passante, statut des services

## Pour aller plus loin

- Voir `03-utilisation/crons.md` pour le déploiement automatisé
- Voir `exemples/LEO.md` pour les dashboards en production
