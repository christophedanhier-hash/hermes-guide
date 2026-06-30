# Table des matières

## 📖 Hermès pour les Nuls

```
┌────────────────────────────────────────────────────────────┐
│  HERMÈS POUR LES NULS                                      │
│  Construire son propre assistant IA avec LEO              │
│                                                            │
│  Partie I  — Découvrir Hermès          🏁                  │
│  Partie II — Configurer son Assistant  ⚙️                  │
│  Partie III — Les Bureaux BAVI         🏛️                  │
│  Partie IV — La Puissance des Skills   🧠                  │
│  Partie V — Dashboards et Monitoring   📊                  │
│  Partie VI — Automatisation et Crons   ⏱️                  │
│  Partie VII — La Partie des Dix        💡                  │
│  Annexes                                📚                 │
└────────────────────────────────────────────────────────────┘
```

---

## Partie I — Découvrir Hermès 🏁
*Commencer par le commencement*

- **[Ch.1 — Un agent IA, c'est quoi ?](01-decouvrir-hermes/ch01-cest-quoi-un-agent.md)**
  - Chatbot vs agent : la différence fondamentale
  - Ce que LEO fait que ChatGPT ne peut pas faire
  - Les briques d'un agent : modèle, outils, mémoire, actions

- **[Ch.2 — Pourquoi Hermès ?](01-decouvrir-hermes/ch02-pourquoi-hermes.md)**
  - Hermes vs Claude Code vs Codex vs OpenCode
  - Multi-provider : DeepSeek, Gemini, Ollama, 15+ autres
  - Skills : le super-pouvoir qui rend Hermes unique
  - Plateformes : Telegram, Discord, Slack, email, et plus

- **[Ch.3 — L'architecture LEO](01-decouvrir-hermes/ch03-architecture-leo.md)**
  - Vue d'ensemble : 3 bots, 3 profils, providers dédiés
  - Le Gateway DeepSeek : pont entre Telegram et l'agent
  - Hiérarchie des providers : quand utiliser quoi
  - Les chiffres clés de LEO (dashboards, crons, skills)

- **[Ch.4 — Installation rapide](01-decouvrir-hermes/ch04-installation-rapide.md)**
  - Installation sur Linux (Debian/Ubuntu)
  - Installation sur Windows (WSL)
  - Premier lancement et configuration minimale
  - Vérification : le diagnostic

---

## Partie II — Configurer son Assistant ⚙️
*Moteur, on tourne !*

- **[Ch.5 — Le Gateway : connecter Telegram](02-configurer/ch05-gateway.md)**
  - Créer un bot Telegram avec @BotFather
  - Configurer le gateway Hermes
  - Gérer les profils : default, leo-copilot, bavi-leo
  - La gestion s6 en environnement Docker

- **[Ch.6 — Providers : le moteur de votre agent](02-configurer/ch06-providers.md)**
  - DeepSeek : le pilier principal
  - Ollama : l'IA locale et gratuite
  - Gemini : le fallback silencieux
  - Hiérarchie et fallback : comment Hermes choisit

- **[Ch.7 — Multi-bots : pourquoi plusieurs valent mieux qu'un](02-configurer/ch07-multi-bots.md)**
  - L'architecture multi-profil de LEO
  - Quand créer un nouveau bot vs tout dans le même
  - Synchronisation de mémoire entre profils
  - Gérer ses tokens et cred pools

- **[Ch.8 — Skills : le super-pouvoir d'Hermès](02-configurer/ch08-skills.md)**
  - Qu'est-ce qu'un skill ?
  - Les 117 skills de LEO : classification et navigation
  - Installer, charger, et utiliser des skills
  - Skills système vs skills utilisateur

- **[Ch.9 — Mémoire persistante](02-configurer/ch09-memoire.md)**
  - Pourquoi un agent a besoin de mémoire
  - Memory vs User Profile
  - Configurer et utiliser la mémoire
  - Le cron sync-memory entre profils

---

## Partie III — Les Bureaux BAVI 🏛️
*La force de l'organisation*

- **[Ch.10 — Architecture bureaux](03-bureaux-bavi/ch10-architecture-bureaux.md)**
  - Le concept BAVI : organiser ses connaissances par bureau
  - Les 10 bureaux : qui fait quoi
  - La gouvernance : comment les bureaux collaborent

- **[Ch.11 — Bureau Michel : l'infrastructure](03-bureaux-bavi/ch11-michel-infra.md)**
  - Déploiement et configuration des workflows
  - Gestion système, watchdogs, scripts
  - La checklist de déploiement

- **[Ch.12 — Bureau Sylvia : les voyages](03-bureaux-bavi/ch12-sylvia-voyages.md)**
  - Le bot voyages dédié (@bavi_leo_voyages_bot)
  - Roadbooks et wiki voyages
  - Agence de voyage complète (camping-car, hôtels, itinéraires)

- **[Ch.13 — Bureau Emile : la pédagogie](03-bureaux-bavi/ch13-emile-pedagogie.md)**
  - Assistant pédagogique pour mémoire de fin d'études
  - Méthodologie d'audit et workflow

- **[Ch.14 — Bureau Robert : le conseil stratégique](03-bureaux-bavi/ch14-robert-conseil.md)**
  - Analyses concurrentielles
  - Recommandations stratégiques IT
  - Documentation T600

- **[Ch.15 — Les autres bureaux](03-bureaux-bavi/ch15-autres-bureaux.md)**
  - Bureau Sophie : pilotage économique et financier
  - Bureau Gérard : documentation technique
  - Bureau Virginie : orchestration médicale
  - Bureau LEO : le fourre-tout personnel
  - Assurance Obligatoire : le bureau transverse

---

## Partie IV — La Puissance des Skills 🧠
*Le savoir-faire réutilisable*

- **[Ch.16 — Skills système](04-skills/ch16-skills-systeme.md)**
  - hermes-agent, hermes-gateway, hermes-profiles
  - Configuration et troubleshooting
  - Les profils multi-agents

- **[Ch.17 — Skills productivité](04-skills/ch17-productivite.md)**
  - Dashboards : hermes-dashboard, dashboard-kpi
  - Documentation : mkdocs-wiki, living-documentation
  - Google Workspace, Airtable, Notion
  - Email : inbox-zero, leo-email-assistant

- **[Ch.18 — Skills DevOps](04-skills/ch18-devops.md)**
  - GitHub PR workflow, code review, issues
  - Code-server VS Code dans le navigateur
  - Déploiement de dashboards

- **[Ch.19 — Skills créatifs](04-skills/ch19-creatifs.md)**
  - ASCII art, architecture diagrams, Excalidraw
  - ComfyUI, p5.js, manim-video
  - Songwriting et musique IA

- **[Ch.20 — Skills recherche et veille](04-skills/ch20-recherche.md)**
  - AI Tech Watch : 17 sources RSS
  - arXiv, blogwatcher, Polymarket
  - Llm-wiki : base de connaissances LLM

- **[Ch.21 — Écrire ses propres skills](04-skills/ch21-ecrire-ses-skills.md)**
  - Le format SKILL.md : frontmatter et contenu
  - Les bonnes pratiques
  - Versionner et partager ses skills

---

## Partie V — Dashboards et Monitoring 📊
*Voir l'invisible*

- **[Ch.22 — L'écosystème de dashboards](05-dashboards/ch22-ecosysteme-dashboards.md)**
  - Architecture : 7 dashboards, HTML statique, GitHub Pages
  - Navigation interconnectée
  - Cycle de vie d'une donnée : du chat Telegram au graphique

- **[Ch.23 — Métriques machines](05-dashboards/ch23-metriques-machines.md)**
  - CPU, RAM, disque, GPU : collecte et visualisation
  - Les 3 machines de LEO : LEO, Yoga, Penguin
  - Alertes et seuils

- **[Ch.24 — Monitoring des crons](05-dashboards/ch24-monitoring-crons.md)**
  - Le tableau de bord des 25 tâches planifiées
  - Historique 7 jours, durée d'exécution, taux de succès
  - Détection des crons bloqués ou en échec

- **[Ch.25 — Budget et tracking](05-dashboards/ch25-budget-tracking.md)**
  - Suivi du solde DeepSeek en temps réel
  - Projection de consommation
  - Dashboards LEO KPI et BAVI LEO KPI

---

## Partie VI — Automatisation et Crons ⏱️
*Que ça roule tout seul*

- **[Ch.26 — Le scheduler Hermes](06-automatisation/ch26-scheduler.md)**
  - no_agent vs LLM-driven : quel mode pour quelle tâche ?
  - Script vs prompt : les critères de choix
  - Syntaxe cron, delivery, workdir

- **[Ch.27 — Les crons horaires](06-automatisation/ch27-crons-horaires.md)**
  - La vague H:00-H:30 : 8 crons qui s'enchaînent
  - Machines KPI, budget, dashboards
  - Le staggered scheduling

- **[Ch.28 — Les crons quotidiens et spéciaux](06-automatisation/ch28-crons-quotidiens.md)**
  - Backup automatique (06:00)
  - Veille IA (08:00)
  - Drive sync (18:00)
  - Classifieur emails (toutes les 15 min)
  - Auto-commit repos (toutes les 2h)

- **[Ch.29 — Watchdogs et alertes](06-automatisation/ch29-watchdogs.md)**
  - Dashboard Watch : vérification automatique du contenu
  - Auto-Heal : détection et correction des erreurs
  - Code-server watchdog
  - Le double filet : Hermes + n8n

- **[Ch.30 — Drive ↔ GitHub Sync](06-automatisation/ch30-drive-sync.md)**
  - Synchronisation bidirectionnelle Drive ↔ GitHub
  - Résolution de conflits
  - Le Drive Guardian dans n8n

---

## Partie VII — La Partie des Dix 💡
*Les listes qui sauvent*

- **[Ch.31 — 10 astuces pour ne pas galérer](07-partie-des-dix/ch31-10-astuces.md)**
  - Les pièges à éviter absolument
  - Astuces de configuration et d'usage quotidien

- **[Ch.32 — 10 commandes à connaître absolument](07-partie-des-dix/ch32-10-commandes.md)**
  - Les essentiels du CLI Hermes
  - Commandes slash en session interactive

- **[Ch.33 — 10 façons d'étendre Hermès](07-partie-des-dix/ch33-10-extensions.md)**
  - MCP servers, plugins, webhooks
  - Intégrations avec d'autres outils

- **[Ch.34 — 10 ressources pour aller plus loin](07-partie-des-dix/ch34-10-ressources.md)**
  - Documentation officielle, skills hub, communauté

---

## Annexes 📚

- **[Annexe A — Glossaire](annexes/glossaire.md)**
- **[Annexe B — Guide de démarrage rapide](annexes/guide-rapide.md)**
- **[Annexe C — Arbre de décision des providers](annexes/arbre-providers.md)**
- **[Annexe D — Check-list déploiement](annexes/checklist-deploiement.md)**
- **[Annexe E — Aide-mémoire des commandes](annexes/commandes.md)**
- **[Annexe F — Exemple : architecture complète de LEO](annexes/exemple-leo-complet.md)**
- **[Annexe G — Troubleshooting](annexes/troubleshooting.md)**

---

**Légende :** 📝 = écrit | 🔄 = en cours | ⬜ = à rédiger
