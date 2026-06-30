# Métriques machines : CPU, RAM, disque, GPU

Un assistant qui tourne 24h/24 a besoin qu'on surveille sa santé. Les métriques machines sont le premier dashboard que LEO a mis en place.

## Pourquoi surveiller ?

```yaml
Risques réels:
  - Disque plein → Hermes plante, plus de logs, plus de sessions
  - RAM épuisée → ralentissements, OOM kill du conteneur
  - GPU saturé → Ollama répond en 5 min au lieu de 2 secondes
  - CPU à 100% → tout est lent, le gateway timeout
```

## Les métriques essentielles

### Disque

```bash
# La métrique la plus critique
df -h /opt/data
# → /dev/sda2  457G   89G  345G   21%  /
# → Si > 80%, agir
```

LEO a 2 disques :
- **SSD** `/dev/sda2` (457 Go) — système + données Hermes
- **HDD** `/dev/sdb2` (1 To) — backups, recovery kit, archives

### RAM

```bash
free -h
# → 22.94 Go total, ~2 Go pour Hermes, ~500 Mo pour n8n
```

Si la RAM utilisée dépasse 85%, les conteneurs Docker risquent l'OOM kill.

### GPU

```bash
nvidia-smi --query-gpu=memory.used,utilization.gpu --format=csv
# → RTX 3050, 8 Go VRAM
# → Utilisé par Ollama pour la classification
```

### Processus

```bash
# Vérifier que tout tourne
docker ps
# → hermes-agent, n8n, ollama : tous UP
```

## Dashboard machines sur LEO

Le dashboard **leo-metrics** affiche en temps réel :

```markdown
| Machine  | CPU | RAM  | Disque | Statut |
|:---------|:---:|:----:|:------:|:------:|
| LEO      | 12% | 4/23 | 89/457 | 🟢     |
| Yoga     | 8%  | 6/16 | 120/512 | 🟢    |
| Penguin  | 15% | 3/6  | 45/128 | 🟢     |
```

Collecte : toutes les heures (cron no_agent, 0€).
Technologie : script bash → JSON → Chart.js → GitHub Pages.

## Alerte automatique

```yaml
Seuils d'alerte:
  Disque > 80%:  🔴 Action immédiate (nettoyage ou extension)
  RAM > 85%:     🟡 Surveillance renforcée
  GPU > 90%:     🟡 Check processus Ollama
  CPU > 90%:     🟡 Vérifier crons en parallèle
```

L'auto-heal détecte ces seuils toutes les 30 minutes.

## 3 machines surveillées

| Machine | OS | RAM | Stockage | Rôle |
|:--------|:---|:---:|:--------:|:-----|
| **LEO** 🖥️ | Ubuntu 26.04 | 23 Go | 457 Go | Serveur principal |
| **Yoga** 💻 | Windows 11 | 16 Go | 512 Go | Machine perso |
| **Penguin** 🐧 | Debian 13 | 6 Go | 128 Go | VS Code + Kilo Code |

## Commandes utiles

```bash
# Vue d'ensemble rapide
htop

# Espace disque en temps réel
watch -n 5 df -h /opt/data

# Logs mémoire Docker
docker stats --no-stream

# Température GPU
nvidia-smi
```
