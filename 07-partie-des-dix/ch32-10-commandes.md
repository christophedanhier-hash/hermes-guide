# 10 commandes à connaître absolument

Les commandes essentielles pour utiliser Hermes au quotidien.

## 1. Lancer le gateway

```bash
hermes gateway run --replace
```

Démarre le gateway Telegram. Le flag `--replace` tue l'ancienne instance avant de lancer la nouvelle.

## 2. Créer un profil

```bash
hermes profile create mon-profil
```

Crée un profil Hermes isolé, avec son propre bot Telegram, sa mémoire, ses skills et ses crons.

## 3. Utiliser un profil spécifique

```bash
hermes -p mon-profil chat
```

Lance une session chat avec le profil `mon-profil`. Utile pour basculer entre Léo, Léo Copilote, Sylvia ou Émile.

## 4. Lister les crons

```bash
hermes cron list
```

Affiche toutes les tâches planifiées, leur état, leur prochaine exécution.

## 5. Créer un cron no_agent (gratuit)

```bash
hermes cron create \
  --name "Vérification disque" \
  --schedule "0 8 * * *" \
  --script /opt/data/scripts/check-disk.sh \
  --no-agent
```

Le flag `--no-agent` = pas de LLM = **0€ par exécution**.

## 6. Voir les logs du gateway

```bash
tail -f /opt/data/logs/gateway.log
```

Indispensable pour comprendre pourquoi le bot ne répond pas, ou debugger une connexion Telegram.

## 7. Modifier la configuration

```bash
hermes config set model.default deepseek-v4-pro
hermes config set display.language fr
```

Modifie le `config.yaml` du profil courant sans éditer le fichier à la main.

## 8. Sauvegarder un fait en mémoire

```bash
hermes memory add "Le serveur est à Bruxelles" --target memory
hermes memory add "Christophe préfère le style concis" --target user
```

`--target memory` = pour le système. `--target user` = pour le profil utilisateur.

## 9. Lister les skills

```bash
hermes skills list
```

Affiche tous les skills disponibles. Vous pouvez charger le détail avec `hermes skills show <nom>`.

## 10. Forcer une exécution de cron

```bash
hermes cron run <id-du-cron>
```

Exécute immédiatement une tâche planifiée, sans attendre son horaire. Utile pour tester un nouveau cron.

## Bonus : la commande magique

```bash
# Tout reconstruire après un crash
# 1. Restaurer le backup GDrive
# 2. Lancer les gateways
hermes gateway run --replace                    # Léo
hermes -p leo-copilot gateway run --replace     # Léo Copilote
hermes -p bavi-leo gateway run --replace        # Sylvia
hermes -p emile gateway run --replace           # Émile
```
