# Chapitre 2 — Pourquoi Hermès ?

> *Il y a une dizaine d'agents IA open source. Pourquoi Hermès est le bon choix.*

---

Vous avez décidé de construire votre propre assistant IA. Bonne décision ! Maintenant, quel framework choisir ? Claude Code ? Codex ? OpenCode ? Il y a une dizaine d'options sur le marché, et Hermès n'est pas le plus connu.

Voici pourquoi LEO tourne sur Hermès — et pourquoi c'est le bon choix pour vous aussi.

## Le paysage des agents IA en 2026

| Agent | Éditeur | Langage | LLM imposé ? | Multi-plateforme ? | Skills ? | Prix |
|:------|:--------|:--------|:-------------|:------------------:|:--------:|:----:|
| **Hermes Agent** | Nous Research | Python | Non 🔓 | ✅ (15+) | ✅ | Gratuit |
| Claude Code | Anthropic | TypeScript | Claude (Anthropic) | ❌ CLI seul | ❌ | Payant |
| Codex CLI | OpenAI | Python (sandbox) | GPT (OpenAI) | ❌ CLI seul | Partiel | Payant |
| OpenCode | Communauté | Python | Non 🔓 | ❌ CLI seul | ❌ | Gratuit |
| Copilot CLI | GitHub | N/A | GPT (Microsoft) | ❌ CLI seul | ❌ | Abonnement |

### Pourquoi les autres ne convenaient pas

**Claude Code** est excellent pour le code, mais :
- Vous devez utiliser Claude exclusivement (pas de DeepSeek, pas d'Ollama)
- Pas de gateway Telegram, Discord, etc. — c'est un outil CLI pur
- Pas de skills réutilisables
- Abonnement payant (20$/mois + consommation)

**Codex CLI** est intéressant mais :
- Fonctionne dans un bac à sable — l'agent ne touche pas vraiment votre système
- Pas de persistance, pas de crons, pas de mémoire entre sessions
- Verrouillé OpenAI

**OpenCode** est open source mais :
- Pas de gateway (aucune plateforme de messagerie)
- Pas de système de skills
- Fonctionnalités limitées comparé à Hermès

### Ce qui rend Hermès unique

#### 1. 🔓 Multi-provider : vous n'êtes pas enfermé

Avec Hermès, vous pouvez utiliser **n'importe quel LLM** — et même les combiner :

```yaml
# Dans config.yaml
model:
  default: deepseek-v4-flash    # Le quotidien, économique
  delegation: deepseek-chat     # Sous-agents pour tâches complexes
fallback_providers:
  - provider: deepseek          # Fallback si le principal plante
    model: deepseek-v4-flash
providers:
  custom:
    ollama:                     # IA locale, gratuite
      base_url: http://localhost:11434/v1
    google:                     # Gemini, fallback gratuit
      base_url: https://generativelanguage.googleapis.com/v1beta/openai/
```

**L'avantage :** vous pouvez avoir un LLM cher et puissant pour le dialogue (DeepSeek), un LLM local et gratuit pour les tâches batch (Ollama), et un fallback en cas de panne (Gemini). Hermès gère la bascule tout seul.

#### 2. 🌐 Multi-plateforme : votre agent partout

| Plateforme | Statut | Usage typique |
|:-----------|:------:|:--------------|
| Telegram | ✅ | Canal principal de LEO |
| Discord | ✅ | Communautés gaming/dev |
| Slack | ✅ | Équipes pro |
| WhatsApp | ✅ | Usage personnel |
| Signal | ✅ | Messagerie sécurisée |
| Email | ✅ | Notifications sortantes |
| SMS | ✅ | Alertes d'urgence |
| API Server | ✅ | Intégration avec vos apps |
| Webhooks | ✅ | Automation tierce |

LEO, lui, communique **uniquement par Telegram** et envoie des **emails en sortie**. Mais votre assistant pourrait être sur Discord, WhatsApp, ou les trois.

#### 3. 🧠 Skills : le super-pouvoir

Un skill, c'est un document qui dit à Hermès : « Voici comment faire X. » Vous écrivez le mode d'emploi une fois, Hermès l'utilise à chaque fois.

```markdown
# skill: deploy-dashboard

1. Générer le fichier index.html avec les données à jour
2. Faire un git add, commit, push
3. Forcer le rebuild GitHub Pages via l'API
4. Vérifier que le dashboard répond HTTP 200
```

LEO a **117 skills** répartis en 22 catégories. Chaque skill encapsule une procédure — déployer un dashboard, envoyer un email, analyser un RSS, etc. Résultat : LEO sait faire des choses qu'on ne lui a jamais montrées, parce qu'il a le mode d'emploi.

> 🦁 **Exemple LEO :** Le skill `dashboard-deployment` contient toute la procédure de déploiement d'un dashboard HTML sur GitHub Pages. LEO peut déployer un nouveau dashboard en 30 secondes, sans erreur, parce que le skill lui dit exactement quoi faire.

#### 4. ⏱️ Cron : votre assistant qui travaille 24/7

Les crons Hermes ne sont pas de simples tâches shell. Chaque cron peut être :

- **Un script pur** (no_agent) — zéro token LLM, exécution directe
- **Un prompt LLM** — l'agent réfléchit et agit
- **Un script + un prompt** — collecte des données puis analyse

LEO a **25 crons actifs** dont 23 en no_agent (0$ de consommation LLM pour les tâches répétitives).

#### 5. 🗂️ Profils et gateways parallèles

Avec Hermès, vous pouvez avoir **plusieurs agents indépendants** sur la même machine :

| Profil | Bot Telegram | Provider | Rôle |
|:-------|:-------------|:---------|:-----|
| `default` | @hermes_leo_bot | DeepSeek Flash | Chat quotidien |
| `leo-copilot` | @hermes_leo_copilot_bot | DeepSeek V4 Pro | Code, infra, n8n |
| `bavi-leo` | @bavi_leo_voyages_bot | DeepSeek Flash | Voyages camping-car |

Chaque profil a son propre gateway, ses propres skills, sa propre mémoire. Et pourtant, ils peuvent partager des informations via un cron `sync-memory`.

## 🦁 Pourquoi Christophe a choisi Hermès

> « J'ai essayé Claude Code — excellent pour le code, mais incapable de m'envoyer un message Telegram ou de classer mes emails. J'ai essayé ChatGPT — il parle bien, mais il ne fait rien. Hermès est le seul qui combine la puissance d'un LLM avec la liberté d'un vrai assistant. »

— Christophe (Tofdan), créateur de LEO

**Le critère décisif :** Hermès a le meilleur rapport **puissance/flexibilité/prix**. Gratuit, open source, multi-provider, multi-plateforme, avec un système de skills qui le rend évolutif à l'infini.

## 📝 À retenir

| Critère | Hermès | Les autres |
|:--------|:------:|:----------:|
| Multi-provider | ✅ | ❌ (enfermé) |
| Multi-plateforme | ✅ (15+) | ❌ (CLI seul) |
| Skills | ✅ (117 dispo) | ❌ |
| Crons | ✅ (avancés) | ❌ |
| Gratuit | ✅ | Souvent payant |
| Open source | ✅ | Variable |

---

**[Chapitre suivant → L'architecture LEO](ch03-architecture-leo.md)**
