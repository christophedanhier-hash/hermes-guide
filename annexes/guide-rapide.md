# Annexe B — Guide de démarrage rapide

> *Installer Hermès et parler à son premier agent en 60 secondes*

---

## Installation

```bash
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
```

## Premier chat

```bash
hermes chat -q "Bonjour, que peux-tu faire ?"
```

## Connexion Telegram

```bash
# 1. Créez un bot avec @BotFather sur Telegram
# 2. Récupérez le token
# 3. Configurez Hermes
hermes gateway setup
```

## Commandes essentielles

| Commande | Action |
|:---------|:-------|
| `hermes` | Lancer en mode interactif |
| `hermes doctor` | Diagnostic complet |
| `hermes model` | Changer de modèle/provider |
| `hermes tools list` | Voir les outils disponibles |
| `hermes skills list` | Voir les skills installés |
| `hermes cron list` | Voir les crons actifs |
| `hermes gateway status` | Voir le statut du gateway |
| `hermes profile list` | Voir les profils |

## Slash commands utiles

En session interactive :

| Commande | Effet |
|:---------|:-------|
| `/new` | Nouvelle session |
| `/retry` | Renvoyer la dernière requête |
| `/undo` | Annuler le dernier échange |
| `/title mon-sujet` | Nommer la session |
| `/skill nom` | Charger un skill |
| `/platforms` | Statut des plateformes |
| `/help` | Liste complète |

## Syntaxe cron rapide

```text
0 6 * * *      → Tous les jours à 6h
30m            → Toutes les 30 min
every 2h       → Toutes les 2h
every monday 9am → Tous les lundis 9h
```

> 🔗 **Documentation complète :** [hermes-agent.nousresearch.com/docs](https://hermes-agent.nousresearch.com/docs)
