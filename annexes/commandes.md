# Aide-mémoire des commandes Hermes (v0.16+)

## Installation et lancement

```bash
# Installer Hermes (méthode recommandée)
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash

# Lancer Hermes en mode interactif
hermes
# ou : hermes chat

# Lancer Hermes avec une requête directe (one-shot)
hermes chat -q "Ton message ici"

# Assistant de configuration interactif
hermes setup

# Choisir modèle/fournisseur
hermes model

# Vérifier l'état de l'installation
hermes doctor

# Afficher la configuration
hermes config
```

## Profils

```bash
# Lister les profils
hermes profile list

# Créer un profil (vide)
hermes profile create <nom>

# Créer un profil (cloner la config actuelle)
hermes profile create <nom> --clone

# Créer un profil (tout cloner)
hermes profile create <nom> --clone-all

# Utiliser un profil par défaut
hermes profile use <nom>

# Voir les détails d'un profil
hermes profile show <nom>

# Supprimer un profil
hermes profile delete <nom>

# Renommer un profil
hermes profile rename <ancien> <nouveau>

# Exporter un profil (tar.gz)
hermes profile export <nom>

# Importer un profil
hermes profile import <fichier>

# Créer un alias de commande (wrapper)
hermes profile alias <nom>

# Lister tous les profils avec leur statut
hermes profile list
```

## Gateways

```bash
# Démarrer un gateway (premier plan)
hermes gateway run

# Installer le gateway comme service (systemd)
hermes gateway install

# Démarrer le service gateway
hermes gateway start

# Arrêter le service gateway
hermes gateway stop

# Redémarrer le service gateway
hermes gateway restart

# Voir le statut
hermes gateway status

# Configurer les plateformes
hermes gateway setup
```

## Crons

```bash
# Lister tous les crons
hermes cron list

# Créer un cron (script) — no_agent
hermes cron create "0 6 * * *" "Script de backup" --script mon-script.sh

# Créer un cron (LLM)
hermes cron create "0 9 * * *" "Analyse les logs d'erreur"

# Créer avec un skill attaché
hermes cron create "every 1h" "Vérifie les flux RSS" --skill blogwatcher

# Voir le statut du scheduler
hermes cron status

# Modifier un cron
hermes cron edit <id> --schedule "every 4h"

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
# Lister les sessions récentes
hermes sessions list

# Explorateur interactif de sessions
hermes sessions browse

# Exporter une session en JSONL
hermes sessions export <fichier>

# Renommer une session
hermes sessions rename <id> <titre>

# Supprimer une session
hermes sessions delete <id>

# Nettoyer les vieilles sessions
hermes sessions prune

# Statistiques du store de sessions
hermes sessions stats
```

## Skills

```bash
# Lister les skills installés
hermes skills list

# Chercher un skill dans le hub
hermes skills search <requête>

# Installer un skill depuis le hub
hermes skills install <id>

# Inspecter un skill sans l'installer
hermes skills inspect <id>

# Configurer les skills par plateforme
hermes skills config

# Vérifier les mises à jour
hermes skills check

# Mettre à jour les skills obsolètes
hermes skills update

# Désinstaller un skill du hub
hermes skills uninstall <id>

# Parcourir tous les skills disponibles
hermes skills browse
```

## Outils (Toolsets)

```bash
# Interface interactive d'activation/désactivation
hermes tools

# Lister tous les outils et leur statut
hermes tools list

# Activer un toolset
hermes tools enable <nom>

# Désactiver un toolset
hermes tools disable <nom>
```

## Serveurs MCP

```bash
# Ajouter un serveur MCP
hermes mcp add <nom> --url <url>
hermes mcp add <nom> --command <cmd>

# Liste des serveurs configurés
hermes mcp list

# Tester la connexion
hermes mcp test <nom>

# Supprimer un serveur
hermes mcp remove <nom>
```

## Credential Pools (Auth)

```bash
# Gestionnaire interactif de credentials
hermes auth

# Ajouter un credential OAuth ou API
hermes auth add <provider>

# Lister les credentials
hermes auth list

# Supprimer un credential
hermes auth remove <provider> <index>

# Réinitialiser l'état d'épuisement
hermes auth reset <provider>
```

## Configuration

```bash
# Voir la configuration actuelle
hermes config

# Éditer config.yaml dans l'éditeur
hermes config edit

# Modifier un paramètre
hermes config set <chemin> <valeur>

# Voir le chemin de config.yaml
hermes config path

# Voir le chemin du fichier .env
hermes config env-path

# Vérifier les options manquantes
hermes config check

# Mettre à jour la config avec les nouvelles options
hermes config migrate
```

## Mise à jour et maintenance

```bash
# Mettre à jour Hermes
hermes update

# Désinstaller Hermes
hermes uninstall

# Voir les logs du gateway
hermes logs

# Dashboard web
hermes dashboard

# Statistiques d'utilisation (tokens/coût)
hermes insights

# Curateur de skills (maintenance automatique)
hermes curator status

# Kanban (file de travail multi-agent)
hermes kanban list
```

## Commandes Slash (en session interactive)

```bash
/new           Session fraîche
/retry         Renvoyer la dernière requête
/undo          Annuler le dernier échange
/title [nom]   Nommer la session
/model         Changer de modèle
/skill <nom>   Charger un skill
/tools         Gérer les outils
/cron          Gérer les crons
/yolo          Contourner l'approbation
/help          Liste complète des commandes
/quit          Quitter
```

## Syntaxe cron rapide

```text
* * * * *
│ │ │ │ │
│ │ │ │ └── Jour semaine (0-7, 0=dim)
│ │ │ └──── Mois (1-12)
│ │ └────── Jour mois (1-31)
│ └──────── Heure (0-23)
└────────── Minute (0-59)

Exemples :
0 6 * * *      → Tous les jours à 06:00
0 */4 * * *    → Toutes les 4h
0 8 * * 1      → Tous les lundis à 08:00
30m            → Toutes les 30 minutes
every 2h       → Toutes les 2 heures (langage naturel)
every monday 9am → Tous les lundis à 9h
```
