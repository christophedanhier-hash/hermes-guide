# Monitoring crons : le tableau de bord des tâches

Avec 30 crons qui tournent 24h/24, il faut un dashboard pour savoir si tout va bien. C'est le rôle du **crons-dashboard**.

## Le tableau de bord des crons

```markdown
| Cron              | Statut | Dernière exécution | Prochaine exécution | Actions |
|:------------------|:------:|:------------------:|:-------------------:|:-------:|
| Budget Check      | 🟢     | 07:58:12           | 19:58:12            | OK      |
| Backup GDrive     | 🟢     | 04:00:03           | 04:00:03 (demain)   | OK      |
| Veille IA         | 🟢     | 07:00:15           | 07:00:15 (demain)   | OK      |
| Dashboard LEO     | 🟢     | 07:30:22           | 08:30:22            | OK      |
| Dashboard Machines| 🟢     | 07:30:25           | 08:30:25            | OK      |
| Classifieur Gmail | 🟢     | 07:45:01           | 08:00:01            | OK      |
| Sync Drive GitHub | 🟢     | 18:00:30           | 18:00:30 (demain)   | OK      |
```

## Comment ça marche

```bash
# 1. Collecte des statuts
python3 /opt/data/scripts/collect_crons_status.py
# → JSON : { "cron_id": { "status": "ok", "last_run": "...", "next_run": "..." } }

# 2. Génération du dashboard
python3 /opt/data/scripts/deploy-crons-dashboard.py
# → HTML avec Chart.js + tableau

# 3. Push sur GitHub Pages
cd /opt/data/crons-dashboard && git push
```

## Indicateurs clés

| Indicateur | Vert | Orange | Rouge |
|:-----------|:----:|:------:|:-----:|
| Taux de succès | >95% | 80-95% | <80% |
| Temps d'exécution | <30s | 30-60s | >60s |
| Dérive horaire | <5min | 5-15min | >15min |
| Erreurs consécutives | 0 | 1-2 | >3 |

## Les 30 crons de LEO

### Horaires (toutes les heures)

```yaml
- Dashboard LEO KPI
- Dashboard Machines
- Dashboard Crons
- Dashboard GitHub
- Dashboard BAVI LEO
- Dashboard n8n
- Dashboard Global
- Budget Check (08:00, 20:00)
```

### Quotidiens

```yaml
- Backup GDrive           → 04:00
- Veille IA               → 07:00 
- Sync Drive → GitHub     → 18:00
- Hermes Update Check     → 09:00
- Budget Snapshot         → 23:00
```

### Haute fréquence

```yaml
- Classifieur Gmail       → Toutes les 15 min
- Auto-heal               → Toutes les 30 min
- Dashboard Watch         → Toutes les 2h
- Drive Watch             → Toutes les 6h
```

### Hebdomadaires

```yaml
- Vérification infra      → Lundi 08:00
- Curator (nettoyage)     → Dimanche 04:00
```

## Gérer les erreurs

```bash
# Voir les logs d'un cron
hermes cron log <id>

# Relancer un cron en échec
hermes cron run <id>

# Suspendre un cron défaillant
hermes cron pause <id>

# Voir les erreurs récentes
tail -50 /opt/data/logs/errors.log
```

## Auto-heal : correction automatique

Quand un cron échoue, l'auto-heal tente de le corriger :

```
1. Détection : cron en erreur 2 fois de suite
2. Diagnostic : erreur de permission ? script manquant ? timeout ?
3. Correction : recopie du script, ajustement du timeout, redémarrage
4. Vérification : relance le cron, vérifie le résultat
5. Si échec → issue GitHub (label "auto-heal")
```

En production, l'auto-heal résout ~80% des problèmes sans intervention humaine.
