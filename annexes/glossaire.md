# Annexe A — Glossaire

> *Les termes techniques décryptés, du débutant à l'expert*

---

**Agent** — Un programme IA qui ne se contente pas de répondre à des questions : il exécute des actions sur votre système (lire des fichiers, envoyer des emails, lancer des scripts). Voir Chapitre 1.

**API** — Interface de programmation. Permet à deux logiciels de communiquer. Exemple : l'API DeepSeek permet à Hermès d'envoyer des requêtes au modèle.

**Agent Profile** — voir *Profil Hermes*.

**BAVI** — L'organisation des connaissances de LEO en bureaux spécialisés (Michel pour l'infra, Sylvia pour les voyages, etc.). Voir Partie III.

**Bot Telegram** — Un compte Telegram automatisé, identifié par un handle en `@nom_bot`. Chaque bot Hermes est lié à un profil.

**Chatbot** — Programme IA qui répond à des questions. Contrairement à un agent, il n'agit pas sur le système.

**CLI** — Command Line Interface (interface en ligne de commande). Le mode `hermes chat` en terminal.

**Context Window** — La mémoire à court terme d'un LLM, limitée en tokens. Hermès compresse automatiquement quand la limite approche.

**Credential Pool** — Groupe de clés API pour un même provider. Hermès peut alterner entre plusieurs clés pour éviter les limites de taux.

**Cron** — Tâche planifiée qui s'exécute automatiquement à intervalles réguliers. Hermès supporte deux modes : `no_agent` (script pur) et `LLM-driven` (avec raisonnement). Voir Partie VI.

**Dashboard** — Page HTML statique qui visualise des métriques (budget, performances, activité). Hébergé sur GitHub Pages.

**DeepSeek** — Fournisseur de LLM principal de LEO. Deux modèles : Flash (économique, dialogue) et V4 Pro (puissant, code/infra).

**Docker** — Technologie de conteneurisation. LEO tourne dans un conteneur Docker supervisé par s6.

**Fallback Provider** — Provider de secours configuré. Si le provider principal échoue, Hermès bascule automatiquement sur le fallback.

**Gateway** — Le service Hermes qui fait le pont entre les plateformes de messagerie (Telegram, Discord, etc.) et l'agent. Voir Chapitre 5.

**Gemini** — LLM de Google. Utilisé par LEO comme fallback gratuit (gemini-2.5-flash) et comme provider principal pour le bot leo-copilot.

**GitHub Pages** — Service d'hébergement gratuit de pages web statiques. Tous les dashboards LEO sont hébergés ici.

**HERMES_HOME** — Variable d'environnement pointant vers le répertoire de configuration d'Hermes (par défaut `~/.hermes/`).

**LLM** — Large Language Model (grand modèle de langage). Le cerveau de l'agent : DeepSeek, Gemini, Ollama, etc.

**LLM-driven** — Mode de cron où l'agent utilise un LLM pour réfléchir avant d'agir. Plus coûteux qu'un script pur.

**Memory** — Système de mémoire persistante d'Hermès. Deux types : `memory` (notes personnelles de l'agent) et `user` (profil de l'utilisateur).

**MCP Server** — Model Context Protocol. Permet d'ajouter des outils externes à Hermès (API, bases de données, services).

**MkDocs** — Générateur de sites statiques. Utilisé pour créer les wikis LEO à partir de fichiers Markdown.

**Multi-profil** — Possibilité d'avoir plusieurs configurations Hermes isolées (profils) sur la même machine, chacune avec son propre gateway, skills et mémoire.

**n8n** — Plateforme d'automatisation low-code. Utilisée par LEO en complément des crons Hermes pour les tâches critiques (retry natif).

**no_agent** — Mode de cron où le script s'exécute directement **sans** LLM. Zéro token consommé, idéal pour les tâches répétitives.

**Ollama** — Logiciel pour exécuter des LLM localement. Gratuit, privé, ne nécessite pas de connexion Internet.

**Open Source** — Logiciel dont le code source est public et modifiable. Hermes Agent, Ollama, MkDocs sont open source.

**Profil Hermes** — Ensemble isolé de configuration : modèle LLM, skills, mémoire, crons, gateway. Permet d'avoir plusieurs agents indépendants.

**Provider** — Fournisseur de LLM (DeepSeek, OpenAI, Anthropic, Ollama, etc.). Hermès est multi-provider.

**s6** — Superviseur de processus. Maintient les gateways Hermes en vie et les redémarre automatiquement en cas de crash. Utilisé dans l'environnement Docker de LEO.

**Scheduler** — Ordonnanceur de tâches intégré à Hermes. Gère les crons avec delivery, retry, et reporting.

**Skill** — Document Markdown qui décrit une procédure réutilisable que l'agent peut charger et suivre. Le super-pouvoir d'Hermès. Voir Partie IV.

**Staggered scheduling** — Technique de planification où les crons sont décalés (H:00, H:05, H:10, etc.) pour éviter la contention et les pics de charge.

**Tailscale** — Réseau privé maillé (mesh VPN). Permet à LEO de superviser les 3 machines à distance.

**Telegram** — Application de messagerie. Canal principal de communication avec LEO.

**Token** — Unité de mesure de la consommation d'un LLM. Un token ≈ 0.75 mot. Le coût d'un LLM se mesure en $ par million de tokens.

**Toolset** — Ensemble d'outils activables dans Hermès (terminal, web, browser, vision, etc.). Voir `hermes tools`.

**Watchdog** — Mécanisme de surveillance qui vérifie périodiquement qu'un service tourne et le relance si nécessaire.

**Webhook** — Mécanisme qui permet à un service d'envoyer une notification HTTP à Hermès quand un événement se produit.

**Wiki** — Site de documentation statique généré par MkDocs. LEO a 5 wikis pour différents domaines.

**Workflow n8n** — Séquence d'étapes automatisées dans n8n. Utilisé pour les tâches critiques avec retry automatique.

---

> **💡 Glossaire vivant** — ce glossaire est mis à jour au fil de l'évolution du guide. N'hésitez pas à suggérer des ajouts.
