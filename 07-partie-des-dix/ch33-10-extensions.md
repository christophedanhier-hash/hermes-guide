# 10 façons d'étendre son Hermès

Hermes Agent est conçu pour être extensible. Voici 10 pistes pour aller plus loin.

## 1. Ajouter un LLM local (Ollama)

```yaml
# Économisez 100% sur les tâches simples
model:
  default: ollama
  provider: ollama
  base_url: http://localhost:11434
```

Ollama + Qwen2.5-7B = classification d'emails gratuite, prototypage rapide, confidentialité totale.

## 2. Créer un bot spécialisé

Un bot par domaine : voyage, finances, documentation, pédagogie. Chacun avec son propre profil, son propre token Telegram, sa propre mémoire.

```bash
hermes profile create bot-voyages
hermes -p bot-voyages config set model.default deepseek-v4-flash
hermes -p bot-voyages gateway run --replace
```

## 3. Connecter Discord

```yaml
# config.yaml
discord:
  enabled: true
  token: VOTRE_TOKEN_DISCORD
```

Hermes peut répondre dans vos serveurs Discord. Utile pour les équipes.

## 4. Automatiser avec n8n

n8n connecte Hermes à 400+ services : email, calendrier, CRM, bases de données, webhooks.

```bash
# Workflow exemple
Trigger: Réception email → Action: Classifier avec Hermes → Sortie: Label Gmail
```

## 5. Dashboard temps réel

Des dashboards HTML statiques déployés sur GitHub Pages, mis à jour par des crons :

```bash
python3 /opt/data/scripts/update_mon_dashboard.py
cd mon-dashboard && git push
# → En ligne en 30 secondes
```

## 6. Synchronisation Drive ↔ GitHub

```bash
# Cron toutes les 6h
python3 /opt/data/scripts/drive-sync.sh
# Google Docs → Markdown → Wiki GitHub Pages
```

Les documents Google sont automatiquement convertis en pages de wiki.

## 7. Skills personnalisés

```bash
mkdir -p ~/.hermes/skills/mes-skills/mon-automatisation/
```

Écrivez vos propres skills en Markdown. Voir le Ch.21 pour le guide complet.

## 8. Partage de mémoire entre profils

```bash
ln -s /opt/data/memories/MEMORY.md /opt/data/profiles/mon-profil/memories/MEMORY.md
```

Tous vos bots partagent la même mémoire. Ce que l'un apprend, les autres le savent.

## 9. Multi-modèles (routage intelligent)

```yaml
fallback_providers: '[{"provider": "gemini", "model": "gemini-2.5-flash"}]'
```

DeepSeek pour le quotidien, Gemini pour les longs contextes (1M tokens), Ollama pour le gratuit. Le meilleur des trois mondes.

## 10. Ajouter une plateforme de messagerie

Hermes supporte : **Telegram**, **Discord**, **WhatsApp**, **Slack**, **Signal**, **Microsoft Teams**, **Google Chat**.

```yaml
# Exemple : activer WhatsApp
whatsapp:
  access_token: VOTRE_TOKEN
  phone_number_id: "..."
```

Chaque plateforme peut avoir son propre ensemble de commandes et de permissions.
