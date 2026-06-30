# Crons horaires : métriques, KPI, dashboards

Les crons horaires sont la cheville ouvrière de LEO. Ils tournent 24h/24 et maintiennent les dashboards à jour sans intervention humaine.

## Principe

Un cron horaire = un script no_agent = **0 € par exécution**.

```bash
hermes cron create \
  --name "Dashboard Machines" \
  --schedule "0 * * * *" \
  --script /opt/data/scripts/update_machines_kpi.py \
  --no-agent
```

Le flag `--no-agent` est essentiel : sans LLM, l'exécution est gratuite.

## Les crons horaires de LEO

```yaml
Toutes les heures (minute 0):
  - Dashboard LEO KPI      → collecte sessions, tokens, budget
  - Dashboard Machines     → CPU, RAM, disque 3 machines
  - Dashboard Crons        → statut 30 crons
  - Dashboard GitHub       → activité repos
  - Dashboard BAVI LEO     → KPIs voyages

Toutes les 30 minutes (minute 30):
  - Auto-heal              → vérification santé système
  - Classifieur Gmail      → nouveaux emails (aussi toutes les 15 min)

Toutes les 15 minutes:
  - Classifieur Gmail      → scan boîte de réception
```

## Staggering (évitement de conflit)

Quand plusieurs crons tournent à la même minute, ils peuvent entrer en conflit (API rate limit, CPU saturé). La solution : **staggering**.

```yaml
# Au lieu de tout lancer à H:00
H:00 → Dashboard LEO KPI
H:05 → Dashboard Machines
H:10 → Dashboard Crons
H:15 → Dashboard GitHub
H:20 → Dashboard BAVI LEO
H:25 → Dashboard n8n
H:30 → Auto-heal
```

Chaque cron démarre 5 minutes après le précédent. Les pics de charge sont lissés.

## Script typique

```python
#!/usr/bin/env python3
# update_machines_kpi.py
import json, subprocess

# Collecte
result = subprocess.run(["df", "-h", "/"], capture_output=True, text=True)
disk_usage = result.stdout.split("\n")[1].split()[4]

result = subprocess.run(["free", "-h"], capture_output=True, text=True)
ram_usage = result.stdout.split("\n")[1].split()[2]

# Génération HTML
html = f"""<!DOCTYPE html>
<html>
<head><title>Machines</title></head>
<body>
  <h1>🖥️ Machines</h1>
  <p>Disque: {disk_usage} | RAM: {ram_usage}</p>
</body>
</html>"""

# Sauvegarde
with open("/tmp/dashboard-machines.html", "w") as f:
    f.write(html)
```

## Vérification

```bash
# Voir les logs du cron
tail -20 /opt/data/profiles/leo-copilot/logs/agent.log

# Vérifier le dashboard en ligne
curl -s https://user.github.io/leo-metrics/ | head -5
```
