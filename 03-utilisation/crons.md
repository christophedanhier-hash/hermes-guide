# Tâches planifiées (crons)

Hermes Agent peut exécuter des actions automatiquement selon un planning. C'est ce qui transforme votre assistant en majordome qui travaille 24/7.

## Principe

```
Cron = tâche planifiée qui s'exécute automatiquement
     │
     ├── Script pur (no_agent) → Aucun LLM, exécution directe 0$ ✅
     ├── LLM sur Ollama local  → Gratuit (votre machine)
     └── LLM sur provider payant → Coût par exécution 💰
```

## Types de crons

### Script pur (no_agent)

Pour les tâches purement techniques : collecte de données, backup, déploiement.

**Avantage :** zéro token LLM consommé, exécution rapide, **0$** à vie.

```bash
# Créer un cron no_agent
hermes cron create "0 6 * * *" "Backup quotidien" --script mon-script.sh
```

**Bonnes pratiques :**
- Le script doit être dans `~/.hermes/scripts/`
- Exit code 0 = succès, non-zero = échec
- Le script a accès à `stdout` qui est livré à l'utilisateur

### LLM sur Ollama (local, gratuit)

Pour les tâches qui nécessitent de la réflexion, sans coût :

```bash
hermes cron create "0 9 * * 1" "Analyse les logs et résume les erreurs" --name "rapport-hebdo" --model qwen2.5:7b
```

### LLM sur provider payant

À éviter pour les crons récurrents. Si vraiment nécessaire, privilégiez une
fréquence faible (hebdomadaire, pas horaire).

## Planification efficace

### Ordre de grandeur des coûts

| Type de cron | Coût par run | Coût / mois (horaire) |
|-------------|-------------|----------------------|
| no_agent (script) | **0$** | **0$** |
| Ollama local | **0$** | **0$** |
| DeepSeek / Gemini | ~0.001-0.01$ | ~7-70$ |

**Règle LEO :** Tout cron récurrent (toutes les heures/jours) doit être `no_agent`
ou tourner sur Ollama. DeepSeek est réservé aux interactions directes.

### Ordonnancement décalé (staggered)

Quand plusieurs crons tournent à la même fréquence, décalez les minutes pour
éviter l'embouteillage :

```
H:00 → machines-kpi   (collecte métriques)
H:05 → budget-check   (solde API)
H:10 → dashboard-KPI  (génération HTML)
H:15 → dashboard-machines
H:20 → monitoring-crons
```

Chaque cron a `~5 min` de fenêtre exclusive.

## Configuration d'un cron

### Syntaxe de planification

| Expression | Signification | Usage |
|------------|--------------|-------|
| `0 * * * *` | Toutes les heures à :00 | Collecte métriques |
| `5 * * * *` | Toutes les heures à :05 | Budget |
| `10 * * * *` | Toutes les heures à :10 | Dashboard KPI |
| `0 6 * * *` | Tous les jours à 06:00 | Backup quotidien |
| `0 8 * * 1` | Tous les lundis à 08:00 | Rapport hebdo |
| `0 18 * * *` | Tous les jours à 18:00 | Sync externe |
| `30m` | Toutes les 30 minutes | Monitoring rapide |

### Exemple concret : backup quotidien

```bash
hermes cron create "0 6 * * *" "Backup quotidien" --script run-backup.sh
```

### Exemple concret : dashboard horaire

```bash
hermes cron create "10 * * * *" "Générer le dashboard" --script run-dashboard.sh
```

## Gestion des crons

```bash
# Lister les crons
hermes cron list

# Modifier un cron
hermes cron edit <id> --schedule "every 4h"

# Mettre en pause
hermes cron pause <id>

# Reprendre
hermes cron resume <id>

# Supprimer
hermes cron remove <id>

# Forcer l'exécution immédiate
hermes cron run <id>

# Voir le statut du scheduler
hermes cron status
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

**Astuce LEO :** Créez un **dashboard de monitoring** des crons (voir
`03-utilisation/dashboards.md`) pour avoir un œil sur l'état de tous vos crons
en un coup d'œil. Ce dashboard peut lui-même être mis à jour par un cron
horaire.

### 🔍 dashboard-watch — surveillance automatique

Un cron `dashboard-watch` (toutes les 2h) vérifie que tous les dashboards sont à jour :

- **HTTP 200** — chaque dashboard répond
- **Âge < 2h** — données fraîches
- **Budget cohérent** — valeur affichée du budget ≈ `budget.json` (écart max 1$)
- **Redeploiement auto** — si stale ou 404, le script relance le déploiement

Le script est dans `scripts/dashboard-watch.py` et son état est sauvegardé dans `metrics/dashboard-watch-state.json`.

### 🛡️ Auto-Heal — cicatrisation automatique (cron agent, H:45)

Depuis le 21/06/2026, un cron **agent-driven** (pas no_agent) tourne toutes les heures à H:45 pour détecter et corriger automatiquement les problèmes connus :

- **Crons en erreur** → détection via `cronjob list`, diagnostic (PATH `gh`, script cassé, import manquant) et correction auto + execution forcée
- **Dashboard HTTP non-200** → redéploiement immédiat
- **budget-webhook down** → redémarrage automatique
- **Disque plein** → alerte

**Patterns auto-réparables :**
| Pattern | Détection | Correction |
|---------|-----------|------------|
| `gh` introuvable | Stderr "gh: command not found" | Patch avec chemin absolu `/opt/data/home/.local/bin/gh` |
| Dashboard 404 | HTTP != 200 | Relance le script de déploiement |
| budget-webhook down | Process manquant | Relance via watchdog |
| Import Python cassé | Traceback d'import | pip install dans le venv |

**Rapport :** envoyé sur Telegram UNIQUEMENT si des problèmes détectés. Silence = tout va bien.

## Pièges à éviter

### 🔴 Ne pas mettre de LLM sur une tâche purement script

Un cron de collecte de métriques (Python pur) **n'a pas besoin** d'un LLM.
Le LLM dirait juste "j'ai exécuté le script, voici le résultat" — gaspillage
de tokens et d'argent.

### 🔴 Gérer l'identité et le token Git

Si votre cron push sur GitHub, l'environnement cron peut ne pas avoir accès
aux credentials GitHub. Deux solutions :

**Solution 1 — Chemin absolu vers gh :**
```python
import subprocess, os
gh_path = "/opt/data/home/.local/bin/gh"
tok = os.environ.get("GH_TOKEN")
if not tok:
    tok = subprocess.run([gh_path, "auth", "token"],
        capture_output=True, text=True).stdout.strip()
remote = f"https://user:{tok}@github.com/user/repo.git"
subprocess.run(["git", "remote", "set-url", "origin", remote])
subprocess.run(["git", "push", "origin", "main"])
```

**Solution 2 — Variable d'environnement :**
Définir `GH_TOKEN` dans le script ou l'environnement du cron.

### 🔴 Attention aux chemins dans l'environnement cron

L'environnement d'exécution d'un cron no_agent est minimal. Utilisez des
**chemins absolus** dans vos scripts. Exemple : `/opt/hermes/.venv/bin/python3`
plutôt que `python3`.

### 🔴 Cross-device move

Dans un script cron, ne pas utiliser `Path.rename()` entre `/tmp/` et
`/opt/data/` — ces répertoires sont souvent sur des filesystems différents.
Utilisez `shutil.move()`.

```python
# ❌ Ne fait pas
tmp.rename(local_path)

# ✅ Fait
import shutil
shutil.move(str(tmp), str(local_path))
```

### 🔴 Vérifier les sorties

Un cron no_agent qui exit 0 mais ne fait rien est silencieux. Pour les
tâches critiques, faites en sorte qu'il produise une sortie utile pour
confirmer que le travail a été fait.

## Pour aller plus loin

- Voir `03-utilisation/dashboards.md` pour le monitoring
- Voir `exemples/LEO.md` pour l'architecture cron complète
