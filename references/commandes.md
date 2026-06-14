# Aide-mémoire des commandes Hermes

## Installation et lancement

```bash
# Lancer Hermes en mode interactif
hermes run

# Lancer Hermes avec un message direct
hermes run -m "Ton message ici"

# Vérifier l'état de l'installation
hermes doctor

# Afficher la configuration
hermes config list
```

## Profils

```bash
# Lister les profils
hermes profile list

# Créer un profil
hermes profile create <nom>

# Utiliser un profil
hermes profile use <nom>

# Supprimer un profil
hermes profile remove <nom>
```

## Gateways

```bash
# Démarrer un gateway
hermes gateway start

# Arrêter un gateway
hermes gateway stop

# Redémarrer un gateway
hermes gateway restart

# Voir le statut
hermes gateway status
```

## Crons

```bash
# Lister tous les crons
hermes cron list

# Créer un cron (script)
hermes cron create --script <script.sh> --schedule "cron" --name "<nom>"

# Créer un cron (LLM)
hermes cron create --prompt "<instruction>" --schedule "cron" --name "<nom>"

# Forcer l'exécution
hermes cron run <id>

# Mettre en pause
hermes cron pause <id>

# Reprendre
hermes cron resume <id>

# Supprimer
hermes cron remove <id>
```

## Sessions

```bash
# Lister les sessions
hermes session list

# Voir le contenu d'une session
hermes session show <id>
```

## Skills

```bash
# Lister les skills disponibles
hermes skills list

# Charger un skill
hermes skills load <nom>

# Créer/mettre à jour un skill
hermes skills create <nom>
```

## Configuration

```bash
# Modifier un paramètre
hermes config set <chemin> <valeur>

# Voir la config complète
cat ~/.hermes/config.yaml
```

## Utilitaires

```bash
# Mettre à jour Hermes
git pull && pip install -e .

# Voir les logs
tail -f ~/.hermes/logs/agent.log

# Voir les logs d'erreur
tail -f ~/.hermes/logs/errors.log
```

## Syntaxe cron rapide

```
* * * * *
│ │ │ │ │
│ │ │ │ └── Jour semaine (0-7, 0=dim)
│ │ │ └──── Mois (1-12)
│ │ └────── Jour mois (1-31)
│ └──────── Heure (0-23)
└────────── Minute (0-59)

Exemples :
0 6 * * *    → Tous les jours à 06:00
0 */4 * * *  → Toutes les 4h
0 8 * * 1    → Tous les lundis à 08:00
30m          → Toutes les 30 minutes
```
