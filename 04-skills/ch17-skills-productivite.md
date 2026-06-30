# Skills productivité : dashboards, wikis, email, budget

Au-delà des skills système, Hermes embarque des skills prêts à l'emploi qui transforment votre assistant en outil de productivité. Voici les plus utiles de l'écosystème LEO.

## Dashboards : visualiser sans backend

Le skill `dashboards` (catégorie productivity) gère la création et le déploiement de tableaux de bord HTML statiques.

### Principe

Un dashboard Hermes est un fichier HTML autonome (zéro JavaScript serveur, zéro backend) :

```python
# 1. Collecter les données → JSON
# 2. Générer un HTML avec Chart.js
# 3. Push sur GitHub Pages → site en ligne
```

Ce fonctionnement minimaliste a un avantage décisif : **tout est gratuit**. GitHub Pages ne coûte rien, les scripts de collecte sont en no_agent.

### Exemple : dashboard KPI

```python
import json, subprocess
from datetime import datetime

# Collecte
sessions = len(json.loads(subprocess.run(
    ["cat", "/opt/data/sessions/sessions.json"],
    capture_output=True, text=True).output).get("sessions", []))

# Génération HTML
html = f"""<!DOCTYPE html>
<html>
<head><title>LEO KPI</title>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script></head>
<body>
<h1>📊 LEO — KPIs</h1>
<p>Sessions : {sessions}</p>
<canvas id="chart"></canvas>
<script>
new Chart(document.getElementById('chart'), {{
    type: 'bar',
    data: {{ labels: ['Sessions'], datasets: [{{ data: [{sessions}] }}] }}
}});
</script></body></html>"""

with open("/tmp/dashboard.html", "w") as f:
    f.write(html)
```

### Les dashboards de LEO (7 en production)

| Dashboard | Contenu | URL |
|:----------|:--------|:----|
| **LEO KPI** | Sessions, tokens, budget, crons | `dashboard-leo/` |
| **BAVI LEO** | KPIs voyages, analyses produites | `bavi-leo-dashboard/` |
| **Machines** | CPU, RAM, disque 3 machines | `leo-metrics/` |
| **Crons** | Statut 30 crons, temps d'exécution | `crons-dashboard/` |
| **GitHub** | Activité repos, commits, issues | `github-dashboard/` |
| **n8n** | Workflows, exécutions, erreurs | `dashboard-n8n/` |
| **Global** | Portail agrégé tous dashboards | `leo-global-dashboard/` |

### Déploiement automatisé

```bash
# Script de collecte + génération
python3 /opt/data/scripts/update_budget_kpi.py

# Push sur GitHub Pages
cd /opt/data/leo-dashboard && git add -A && git commit -m "màj dashboard" && git push
```

## Wikis : la documentation vivante

Le skill `mkdocs-wiki` permet de créer et maintenir des wikis MkDocs déployés sur GitHub Pages.

### Créer un wiki

```bash
# 1. Créer le dépôt sur GitHub
# 2. Cloner en local
git clone https://github.com/christophedanhier-hash/mon-wiki.git
cd mon-wiki

# 3. Initialiser MkDocs
pip install mkdocs mkdocs-material
mkdocs new .

# 4. Configurer le thème
cat > mkdocs.yml << EOF
site_name: Mon Wiki
theme: material
EOF

# 5. Écrire les pages
# docs/index.md, docs/sujet1.md, ...

# 6. Déployer
mkdocs gh-deploy
```

### Les 5 wikis de LEO

| Wiki | Contenu | URL |
|:-----|:--------|:----|
| **BAVI_LEO** | Documentation des bureaux | `christophedanhier-hash.github.io/BAVI_LEO/` |
| **Hermès** | Documentation Hermes Agent | `christophedanhier-hash.github.io/hermes-wiki/` |
| **Voyages** | Roadbooks camping-car | `christophedanhier-hash.github.io/voyages-wiki/` |
| **Émile** | Mémoire universitaire | `christophedanhier-hash.github.io/emile-wiki/` |
| **OCA** | Télescope T600 | `christophedanhier-hash.github.io/wiki-oca/` |

### Synchronisation Drive → Wiki

Un cron transforme automatiquement les documents Google Docs en pages de wiki :

```bash
# Script de sync (toutes les 6h)
python3 /opt/data/scripts/drive-sync.sh
# Convertit les .docx en .md
# Commit + push sur le wiki GitHub
```

## Email : la classification inbox zéro

Le skill `gmail-inbox-zero` automatise la classification des emails entrants via Ollama (modèle local, gratuit).

### Fonctionnement

```yaml
1. Toutes les 15 minutes, le cron scanne les emails NON LUS
2. Pour chaque email, Ollama le classe en 9 catégories
3. Un label Gmail est appliqué automatiquement
4. Les emails lus depuis > 2 jours sont archivés
```

### Les 9 catégories

| Catégorie | Label Gmail | Type |
|:----------|:------------|:------|
| 👑 **VIP** | `CAT_VIP` | Christophe, famille, direction |
| ⚙️ **Admin** | `CAT_Admin` | Factures, administrations |
| 💰 **Finances** | `CAT_Finances` | Banques, assurances, impôts |
| 🤖 **IA & Tech** | `CAT_IA-Tech` | News tech, newsletters |
| 🧭 **Voyages** | `CAT_Voyages` | Réservations, billets |
| 🛒 **Achats** | `CAT_Achats` | Commandes, livraisons |
| 🏠 **Maison** | `CAT_Maison` | Énergie, travaux |
| 👨‍👩‍👧‍👦 **Famille** | `CAT_Famille` | Filles, amis |
| 🔭 **Astro** | `CAT_Astro` | Observatoire |

### Règle d'or

Les labels ne sont **appliqués qu'une seule fois**. Pas de re-classification en masse. Une fois qu'un email a son label, il reste dans la boîte de réception jusqu'à ce qu'il soit lu, puis archivé après 2 jours.

```python
# Ne JAMAIS réappliquer les labels en masse
if email.label_ids:
    pass  # Déjà classifié, on ne touche pas
```

### Configuration

```bash
# Clé API Google (OAuth)
# Dans .env
GOOGLE_TOKEN_PATH=/opt/data/google_token.json

# Le cron est déjà configuré dans le profil leo-copilot
# Exécution : toutes les 15 minutes
```

## Budget : le suivi des coûts DeepSeek

Le skill `deepseek-budget` suit la consommation de l'API DeepSeek en temps réel.

### Dashboard budget

```bash
# Exécution : 08:00 et 20:00 (cron)
python3 /opt/data/scripts/diag_budget.py

# Alerte si solde < 10€
# Push sur le dashboard LEO
```

### Chiffres LEO

| Métrique | Valeur |
|:---------|:------:|
| Solde | $60.31 |
| Dépense/jour | $0.03 |
| Coût mensuel | ~$1.50 |
| Jours restants | >6 ans |

Le secret de ce coût ridicule : classification avec Ollama (0€), crons en no_agent (0€), et DeepSeek Flash pour l'essentiel (~0,05€/jour).

### Alertes automatiques

```yaml
Conditions d'alerte:
  - Solde < 10€ → notification Telegram
  - Dépense > 1€/jour → check anomaly
  - Erreur API DeepSeek → fallback Gemini
```

## En résumé

| Skill | Utilité | Coût |
|:------|:--------|:----:|
| **Dashboards** | Visualisation temps réel | 0€ (GH Pages) |
| **Wikis** | Documentation MkDocs | 0€ (GH Pages) |
| **Gmail classifier** | Inbox zéro automatique | 0€ (Ollama) |
| **Budget** | Suivi coûts DeepSeek | 0€ (no_agent) |

## Voir aussi

- **Ch.22** : Dashboards et monitoring (Partie V)
- **Ch.26** : Crons — automatisation (Partie VI)
- **Annexe A** : Glossaire
