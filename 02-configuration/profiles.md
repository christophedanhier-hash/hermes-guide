# Profils, gateways et skills

## Profils

Un **profil** est l'identité de votre assistant. Il définit quel LLM utiliser, quels outils sont disponibles, et comment il se comporte.

```yaml
# Exemple : default
profiles:
  default:
    model: deepseek-chat
    provider: deepseek
    gateway: telegram
```

### Règle LEO : un seul profil

> *"Un seul profil, un seul gateway, tout dedans."*

La tentation est grande de créer un profil par usage (un pour les conversations, un pour le batch, un de secours). **Ne faites pas ça.** Chaque profil supplémentaire ajoute de la complexité et des points de défaillance.

- **Un seul profil** (`default`) — tout votre assistant vit dedans
- **plusieurs providers** au sein du même profil (DeepSeek + Ollama + Gemini)
- **Zéro bascule de profil** — la fiabilité avant tout

### Commandes profils

```bash
# Lister les profils
hermes profile list

# Utiliser un profil
hermes profile use default
```

## Gateways

Un **gateway** est le canal par lequel vous communiquez avec votre assistant.

### Gateway Telegram (recommandé)

Permet de parler à votre assistant depuis votre téléphone.

1. Créez un bot Telegram via [@BotFather](https://t.me/botfather)
2. Notez le token du bot
3. Configurez dans config.yaml :

```yaml
gateways:
  telegram:
    bot_token: "******"
    allowed_users:
      - "votre_username"
      - 123456789  # Votre user ID Telegram
```

4. Lancez le gateway :

```bash
hermes gateway start
```

### Gateway Discord

```yaml
gateways:
  discord:
    bot_token: "******"
```

### Gateway local (terminal)

```bash
# Mode terminal interactif (aucune configuration)
hermes run
```

## Skills

Les **skills** sont des procédures que l'assistant charge pour savoir comment effectuer des tâches spécifiques. C'est la mémoire procédurale de votre assistant.

### Structure d'un skill

Un skill est un fichier `SKILL.md` dans le dossier `skills/` :

```markdown
---
name: mon-skill
description: "Faire X quand Y se produit"
---

# Mon Skill

## Quand l'utiliser
Quand [condition], faire [action].

## Procédure
1. Étape 1
2. Étape 2
3. Vérifier le résultat

## Pièges
- Attention à [piège connu]
```

### Skills essentiels (exemple LEO)

| Skill | Description |
|-------|-------------|
| `living-documentation` | Tenir la documentation à jour |
| `budget-tracking` | Suivi des coûts LLM |
| `leo-architecture` | Architecture et règles de fonctionnement |
| `routage-llm` | Quel LLM utiliser pour quelle tâche |
| `system-management` | Gestion des machines distantes |

### Bonnes pratiques

- **Un skill = une responsabilité** (pas de fourre-tout)
- **Versionnez** les skills (ils évoluent avec votre assistant)
- **Stockez les corrections dans les skills**, pas en mémoire passagère
- **Patchez** un skill obsolète plutôt que d'en créer un nouveau

## Pour aller plus loin

- [Documentation Hermes : Skills](https://hermes-agent.nousresearch.com/docs)
- Voir `02-configuration/providers.md` pour la configuration LLM
- Voir `exemples/LEO.md` pour l'architecture complète
