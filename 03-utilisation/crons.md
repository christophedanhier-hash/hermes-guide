# Tâches planifiées (crons)

Hermes Agent peut exécuter des actions automatiquement selon un planning. C'est ce qui transforme votre assistant en majordome qui travaille 24/7.

## Principe

```
Cron = tâche planifiée qui s'exécute automatiquement
     │
     ├── Script pur (no_agent) → Aucun LLM, exécution directe ✅
     └── LLM-driven → L'assistant réfléchit puis agit 🧠
```

## Types de crons

### Script pur (no_agent)

Pour les tâches purement techniques : collecte de données, backup, déploiement.

**Avantage :** zéro token LLM consommé, exécution rapide.

```bash
# Créer un cron no_agent
hermes cron create --script mon-script.sh --schedule "0 6 * * *" --name "mon-backup"
```

**Bonnes pratiques :**
- Le script doit être dans `~/.hermes/scripts/`
- Exit code 0 = succès, non-zero = échec
- Le script a accès à `stdout` qui est livré à l'utilisateur

### LLM-driven

Pour les tâches qui nécessitent de la réflexion : analyse, résumé, rédaction.

```bash
# Créer un cron LLM
hermes cron create --prompt "Analyse les logs et résume les erreurs" --schedule "0 9 * * 1" --name "rapport-hebdo"
```

**Conseil :** Pour les crons LLM, utilisez de préférence un LLM local gratuit (Ollama) plutôt qu'un provider payant à chaque exécution.

## Configuration d'un cron

### Syntaxe de planification

| Expression | Signification | Exemple |
|------------|--------------|---------|
| `0 6 * * *` | Tous les jours à 06:00 | Backup quotidien |
| `0 */4 * * *` | Toutes les 4h | Dashboard |
| `0 8 * * 1` | Tous les lundis à 08:00 | Rapport hebdo |
| `30m` | Toutes les 30 minutes | Monitoring rapide |

### Exemple concret : backup quotidien

```bash
hermes cron create \
  --script run-backup.sh \
  --schedule "0 6 * * *" \
  --name "daily-backup" \
  --no-agent
```

### Exemple concret : rapport budget

```bash
hermes cron create \
  --prompt "Vérifie le solde DeepSeek et enregistre le relevé" \
  --schedule "0 7 * * *" \
  --name "budget-check" \
  --model "qwen2.5:7b" \
  --provider "custom:ollama"
```

## Gestion des crons

```bash
# Lister les crons
hermes cron list

# Voir le détail d'un cron
hermes cron show <id>

# Mettre en pause
hermes cron pause <id>

# Reprendre
hermes cron resume <id>

# Supprimer
hermes cron remove <id>

# Forcer l'exécution immédiate
hermes cron run <id>
```

## Surveillance

Hermes Agent enregistre chaque exécution de cron avec :
- Son statut (ok/error)
- Sa sortie (stdout)
- Horodatage

Ces informations sont consultables :

```bash
# Voir les logs d'un cron
cat ~/.hermes/cron/output/<id>/*.md
```

**Astuce LEO :** Créez un **dashboard de monitoring** des crons (voir `03-utilisation/dashboards.md`) pour avoir un œil sur l'état de tous vos crons en un coup d'œil.

## Pièges à éviter

### 🔴 Ne pas mettre de LLM sur une tâche purement script

Un cron de collecte de métriques (Python pur) **n'a pas besoin** d'un LLM. Le LLM dirait juste "j'ai exécuté le script, voici le résultat" — gaspillage de tokens.

### 🔴 Gérer l'identité Git pour les push

Si votre cron push sur GitHub, configurez l'identité explicitement dans le script :

```python
subprocess.run(["git", "config", "user.name", "MonAssistant"])
subprocess.run(["git", "config", "user.email", "assistant@exemple.com"])
```

### 🔴 Attention aux chemins dans l'environnement cron

L'environnement d'exécution d'un cron no_agent est minimal. Utilisez des **chemins absolus** dans vos scripts.

### 🔴 Vérifier les sorties

Un cron no_agent qui exit 0 mais ne fait rien est silencieux. Pour les tâches critiques, faites en sorte qu'il produise une sortie utile pour confirmer que le travail a été fait.

## Pour aller plus loin

- Voir `03-utilisation/dashboards.md` pour le monitoring
- Voir `exemples/LEO.md` pour l'architecture cron complète
