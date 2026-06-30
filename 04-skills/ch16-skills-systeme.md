# Skills système : hermes-agent, gateway, profils

Certains skills sont si fondamentaux qu'ils méritent un chapitre à eux seuls. Ce sont les skills qui permettent de configurer, dépanner et étendre Hermes lui-même.

## hermes-agent : le méta-skill

Le skill `hermes-agent` est le **mode d'emploi d'Hermes par Hermes**. Quand vous demandez "comment ajouter un provider ?" ou "comment créer un profil ?", Hermes charge ce skill pour répondre.

Il couvre :

| Sujet | Exemple d'utilisation |
|:------|:----------------------|
| Configuration | Modifier config.yaml, ajouter un provider |
| Profils | Créer, lister, supprimer un profil |
| Gateway | Démarrer/arrêter un gateway, connecter Telegram |
| Mémoire | Consulter, modifier MEMORY.md et USER.md |
| Skills | Installer, créer, lister les skills |
| Crons | Planifier des tâches, mode no_agent |

### Exemple : configurer un nouveau provider

```bash
hermes config set model.default gemini-2.5-flash
hermes config set model.provider gemini
# Ajouter la clé API dans .env
echo "GEMINI_API_KEY=*** >>
```

### Exemple : créer un profil

```bash
hermes profile create mon-profil
# Configurer le profil
hermes -p mon-profil config set model.default deepseek-v4-flash
# Démarrer le gateway
hermes -p mon-profil gateway run
```

## Gateway : le pont vers les plateformes

Le skill `gateway` dans `hermes-agent` gère la connexion aux plateformes de messagerie.

### Telegram (le plus courant)

```yaml
# Dans le .env du profil
TELEGRAM_BOT_TOKEN=*** 
TELEGRAM_ALLOWED_USERS=8718957859
```

```bash
# Démarrer le gateway
hermes gateway run

# Avec un profil spécifique
hermes -p leo-copilot gateway run

# Remplacer un gateway existant
hermes gateway run --replace
```

### Architecture du gateway

Quand vous lancez `hermes gateway run`, Hermes :

1. Lit la configuration du profil
2. Connecte chaque plateforme activée (Telegram, Discord, etc.)
3. Écoute les messages entrants
4. Pour chaque message : charge le contexte, appelle le LLM, exécute les outils, renvoie la réponse
5. Gère le cycle de vie : reconnexion, heartbeat, shutdown

```
Message entrant
     │
     ▼
  Gateway ──→ Session ──→ Agent ──→ LLM
     │                                   
     ▼                                     
  Réponse envoyée
```

### Plusieurs gateways, plusieurs bots

Chaque profil peut avoir son propre gateway avec son propre token Telegram :

```bash
# Terminal 1 : Léo (default)
hermes gateway run

# Terminal 2 : Léo Copilote
hermes -p leo-copilot gateway run

# Terminal 3 : Sylvia
hermes -p bavi-leo gateway run
```

## Profils : l'isolation par design

Le skill `profils` documente comment organiser des instances Hermes indépendantes.

### Cas d'usage typiques

| Profil | Usage | Provider | Token Telegram |
|:-------|:------|:---------|:---------------|
| `default` | Assistant principal | DeepSeek V4 Flash | `881242...` |
| `leo-copilot` | Infrastructure | DeepSeek V4 Pro | `899720...` |
| `bavi-leo` | Voyages | DeepSeek V4 Flash | `885780...` |
| `emile` | Pédagogie | DeepSeek Flash + Gemini | `890688...` |

### Structure d'un profil

```bash
~/.hermes/profiles/mon-profil/
├── config.yaml     # Configuration du profil
├── .env            # Variables d'environnement (tokens, clés API)
├── SOUL.md         # Personnalité et règles
├── memories/       # Mémoire persistante
├── skills/         # Skills synchronisés
├── cron/           # Tâches planifiées
└── sessions/       # Historique des conversations
```

### Synchronisation entre profils

Les skills peuvent être synchronisés d'un profil source vers d'autres profils :

```yaml
# Dans le profil source (default)
skills:
  sync_to:
    - leo-copilot
    - bavi-leo
    - emile
```

## Mémoire : le skill qui retient tout

Le sous-skill `memory` permet de lire et écrire dans la mémoire persistante.

### Lire la mémoire

```bash
# Voir la mémoire système
cat ~/.hermes/memories/MEMORY.md

# Voir le profil utilisateur
cat ~/.hermes/memories/USER.md
```

### Écrire dans la mémoire

```bash
# Ajouter une information
hermes memory add "Le serveur est à Bruxelles" --target memory

# Ajouter une préférence utilisateur
hermes memory add "Christophe préfère les réponses concises" --target user
```

### Partage de mémoire entre profils

```bash
# Créer un lien symbolique pour partager
ln -s /opt/data/memories/MEMORY.md /opt/data/profiles/leo-copilot/memories/MEMORY.md
ln -s /opt/data/memories/USER.md /opt/data/profiles/leo-copilot/memories/USER.md
```

Ainsi, quand un profil apprend quelque chose, les autres en bénéficient immédiatement.

## Crons : l'automatisation sans surveillance

Le skill `cron` documente la planification de tâches.

### Créer un cron simple (script only, 0€)

```bash
hermes cron create \
  --name "Vérification disque" \
  --schedule "0 8 * * *" \
  --script /opt/data/scripts/check-disk.sh \
  --no-agent
```

Le flag `--no-agent` exécute le script **sans LLM** → coût 0€.

### Créer un cron avec agent (LLM)

```bash
hermes cron create \
  --name "Résumé veille" \
  --schedule "0 7 * * *" \
  --prompt "Analyse les articles dans /data/veille/ et fais un résumé"
```

### Lister et gérer les crons

```bash
hermes cron list
hermes cron pause <id>
hermes cron resume <id>
hermes cron remove <id>
hermes cron run <id>    # Exécution immédiate
```

## En résumé

| Skill | À retenir |
|:------|:----------|
| **hermes-agent** | Le mode d'emploi d'Hermes par Hermes |
| **Gateway** | Pont vers Telegram, Discord, etc. |
| **Profil** | Instance isolée avec sa propre config |
| **Mémoire** | Persistance cross-session |
| **Cron** | Tâches planifiées (no_agent = 0€) |

## Voir aussi

- **Ch.5** : Gateway et profils (configuration détaillée)
- **Ch.9** : Mémoire persistante
- **Ch.26** : Crons avancés
- **Annexe B** : Guide de démarrage rapide
