# Skills DevOps : déploiement, backup, monitoring

Les skills DevOps sont ceux qui font tourner LEO 24h/24 sans intervention humaine. Déploiement, sauvegarde, surveillance — tout est automatisé.

## Déploiement : installer un service en un clic

Le skill `deployment` (catégorie infrastructure) documente le déploiement des services sur le serveur LEO.

### Déploiement Docker

```bash
# Lancer un nouveau conteneur
docker run -d --name mon-service \
  --restart unless-stopped \
  --network host \
  mon-image:latest

# Vérifier
docker ps | grep mon-service
docker logs mon-service --tail 20
```

### Déploiement n8n

```bash
# Script de déploiement
bash /opt/data/scripts/run-n8n.sh

# Mise à jour
bash /opt/data/scripts/update-n8n.sh
```

Le script `run-n8n.sh` configure automatiquement :
- Le réseau en mode `host` (évite le bug de login 401 du proxy Docker)
- La base SQLite dans un volume persistant
- Les variables d'environnement (clés API, tokens)

### Déploiement Nginx + Cloudflare

```bash
# Configurer un nouveau site
sudo nano /etc/nginx/sites-available/mon-site.be
sudo ln -s /etc/nginx/sites-available/mon-site.be /etc/nginx/sites-enabled/
sudo nginx -t && sudo systemctl reload nginx

# Configurer le tunnel Cloudflare
cloudflared tunnel create mon-tunnel
cloudflared tunnel route dns mon-tunnel mon-site.be
```

### Déploiement de dashboard

```bash
# 1. Générer le HTML
python3 /opt/data/scripts/update_mon_dashboard.py

# 2. Push sur GitHub Pages
cd /opt/data/mon-dashboard
git add -A && git commit -m "màj $(date +%Y-%m-%d)" && git push

# 3. Vérifier
curl -s -o /dev/null -w "%{http_code}" https://user.github.io/mon-dashboard/
200 ✅
```

## Backup : ne jamais rien perdre

Le skill `backup` (catégorie infrastructure) couvre la stratégie de sauvegarde de LEO : **3 couches** pour une sécurité maximale.

### Couche 1 — Snapshots quotidiens (automatisé)

```bash
# Tous les jours à 04:00
python3 /opt/data/scripts/hermes-backup.py

# Ce script archive :
# - Tous les profils (default, leo-copilot, bavi-leo, emile)
# - La mémoire (MEMORY.md, USER.md)
# - Les scripts customs
# - Les sessions et configurations
# - Les tokens (.env, credentials_vault.json)

# Destination : local + Google Drive (Hermes_Christophe/backups/)
# Rétention : 7 jours
```

### Couche 2 — Recovery Kit (manuel)

```bash
# Kit de récupération d'urgence
/opt/data/recovery-kit/
├── rebuild.sh          # Script de reconstruction
├── docker-commands.md  # Commandes Docker essentielles
└── secrets.b64         # Tokens chiffrés

# Temps de récupération : ~30 minutes
```

### Couche 3 — Image système (hebdomadaire)

```bash
# Image complète du disque système
# Exécution : dimanche 03:00
fsarchiver savefs /mnt/data/recovery/couche3/leo-root.fs /dev/sda2

# Taille : ~30-40 Go
# Temps de récupération : ~1-2 heures
```

### Vérifier un backup

```bash
# Lister les backups locaux
ls -la /opt/data/backups/

# Vérifier le contenu d'un backup
tar -tzf /opt/data/backups/leo-backup-YYYY-MM-DD.tar.gz | head -20

# Vérifier les backups GDrive
# Via le dashboard ou le script
python3 /opt/data/scripts/hermes-backup.py --check
```

## Monitoring : savoir avant que ça casse

Le skill `monitoring` (catégorie infrastructure) regroupe tous les outils de surveillance de LEO.

### Auto-heal (toutes les 30 minutes)

```bash
# Vérifie automatiquement :
# ✅ Crons : 19/19 OK ?
# ✅ Ollama : UP ?
# ✅ n8n : healthz 200 ?
# ✅ Docker : 3/3 conteneurs up ?
# ✅ Disque : < 80% utilisé ?
# ✅ Tokens Google : OK ?
# ❌ Si problème → tentative de correction automatique
# ❌ Si pas de correction possible → issue GitHub (label auto-heal)
```

### Watchdogs (en continu)

```bash
# Scripts watchdogs
/opt/data/scripts/run-all-watchdogs.sh
# Surveille en continu :
# - code-server (VS Code web)
# - code-server-tunnel
# - n8n-healthcheck
# - n8n-dashboard
# - dashboard-watch (vérifie tous les dashboards)
```

### Vérification manuelle

```bash
# Statut des conteneurs
docker ps

# Logs Hermes
tail -f /opt/data/logs/agent.log
tail -f /opt/data/logs/gateway.log

# Logs crons
tail -f /opt/data/profiles/leo-copilot/logs/agent.log

# Dashboard de monitoring
# → https://user.github.io/leo-global-dashboard/
```

### Les 7 dashboards de monitoring

| Dashboard | Fréquence | Vérifie |
|:----------|:---------:|:--------|
| **LEO KPI** | Horaire | Sessions, budget, crons |
| **Machines** | Horaire | CPU, RAM, disque 3 machines |
| **Crons** | Horaire | Statut 30 crons |
| **GitHub** | Horaire | Commits, issues |
| **n8n** | 15 min | Workflows, exécutions |
| **BAVI LEO** | Horaire | KPIs voyages |
| **Global** | Horaire | Portail agrégé |

## La règle d'or

```yaml
Vérification AVANT livraison:
  - curl -s -o /dev/null -w "%{http_code}" <url>  # → 200
  - grep "valeur" <fichier>                         # → trouvé
  - dashboard-watch --check                         # → all green
```

Ne jamais dire "c'est fait" sans avoir vérifié que ça marche.

## En résumé

| Tâche | Skill | Automatisation |
|:------|:------|:---------------|
| Installer un service | `deployment` | Manuel + scripts |
| Sauvegarder | `backup` | Quotidien (04:00) |
| Surveiller | `monitoring` | Continue (auto-heal) |
| Vérifier | `curl` + `grep` | Avant chaque livraison |

## Voir aussi

- **Ch.11** : Bureau Michel (infrastructure)
- **Ch.22** : Dashboards et monitoring
- **Ch.26** : Crons d'automatisation
- **Ch.29** : Watchdogs et alertes
