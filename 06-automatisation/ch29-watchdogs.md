# Watchdogs et alertes

Les watchdogs sont des scripts qui surveillent en continu l'état des services et alertent en cas de problème. C'est le système immunitaire de LEO.

## Principe

Un watchdog = un script qui tourne régulièrement et vérifie qu'un service répond.

```bash
# Watchdog typique
#!/bin/bash
# Vérifie que n8n répond
if ! curl -s http://localhost:5678/healthz > /dev/null; then
    echo "❌ n8n ne répond pas"
    # Tentative de redémarrage
    docker restart n8n
    # Notification
    hermes memory add "n8n relancé le $(date)" --target memory
fi
```

## Les watchdogs de LEO

```yaml
Toutes les 30 minutes:
  - 🩺 Auto-heal complet       → crons, Ollama, n8n, Docker, disque, tokens
  - 📧 Classifieur Gmail       → nouveaux emails à classer

Toutes les 2 heures:
  - 📊 Dashboard Watch         → vérifie que les 7 dashboards répondent
  - 🔄 Dashboard redeploy      → redéploie si un dashboard est obsolète

Toutes les 6 heures:
  - 🔭 Drive Watch             → détecte les changements dans Google Drive

Tous les jours:
  - 🤖 Hermes Update Check     → nouvelle version disponible ?
  - 💰 Budget Check            → solde suffisant ?
```

## Auto-heal : le watchdog principal

```yaml
Vérifications:
  ✅ Crons:        19/19 OK ?
  ✅ Ollama:       qwen2.5:7b responsive ?
  ✅ n8n:          healthz 200 ?
  ✅ Docker:       3/3 conteneurs UP ?
  ✅ Disque:       < 80% utilisé ?
  ✅ Token LEO:    Google API OK ?
  ❌ Token Christophe: invalid_grant (à ré-autoriser manuellement)

En cas d'échec:
  1. Tentative de correction automatique
  2. Si réussi → log + continue
  3. Si échec → issue GitHub (label auto-heal)
```

## Dashboard Watch

Le Dashboard Watch vérifie que les 7 dashboards sont en ligne et à jour :

```bash
for dashboard in leo-kpi machines crons github n8n bavi-leo global; do
    code=$(curl -s -o /dev/null -w "%{http_code}" \
        "https://user.github.io/dashboard-${dashboard}/")
    if [ "$code" != "200" ]; then
        echo "❌ ${dashboard}: HTTP ${code}"
        # Redéploiement automatique
        python3 "/opt/data/scripts/deploy-${dashboard}.py"
    else
        echo "✅ ${dashboard}: HTTP 200"
    fi
done
```

## Notifications

```yaml
Canaux de notification:
  - Telegram DM      → alertes critiques (disque, budget, panne)
  - GitHub Issues    → problèmes non critiques (auto-heal, watchdogs)
  - Dashboard        → tous les statuts en temps réel
  
Règles:
  - Une alerte = une issue GitHub
  - Label "auto-heal" si corrigé automatiquement
  - Pas de spam : pas de notification pour les succès
```
