# Guide Hermes Agent — L'IA qui travaille pour vous

Bienvenue dans le guide d'installation et d'utilisation d'**Hermes Agent**, la plateforme d'agent IA open source qui vous permet de déléguer des tâches à un assistant personnel automatisé.

Ce guide vous accompagne pas à pas, de l'installation à l'utilisation avancée, en prenant **LEO** (l'assistant de Christophe) comme exemple concret.

## Pourquoi Hermes Agent ?

Hermes Agent est un agent IA qui peut :

- **Exécuter des tâches** — écrire du code, analyser des documents, gérer des emails
- **Planifier des actions** — backups quotidiens, collecte de métriques, rapports automatiques
- **S'intégrer partout** — Telegram, email, Google Drive, GitHub, et bien plus
- **Travailler sans vous** — une fois configuré, il tourne 24/7

Contrairement à ChatGPT ou Claude (simples chatbots), Hermes Agent **agit** : il a accès à vos outils, fichiers et services.

## La philosophie LEO

> *"Un assistant n'est pas un chatbot. C'est quelqu'un qui anticipe, qui agit, et qui ne vous fait pas répéter deux fois."*

LEO a été construit pour être :
- **Efficace** — pas de blabla, des actions
- **Fiable** — des garde-fous contre les erreurs
- **Économe** — utilise le meilleur LLM pour chaque tâche (local gratuit ou cloud payant)
- **Discret** — tourne en arrière-plan, ne dérange que quand c'est important

## Structure du guide

```
📖 README.md               → Ce fichier — introduction et philosophie
📁 01-installation/
   ├── linux.md            → Installation sur Debian/Ubuntu
   └── windows.md          → Installation sur Windows (WSL)
📁 02-configuration/
   ├── providers.md        → Configuration LLM (DeepSeek, Ollama, Gemini)
   └── profiles.md         → Profils, gateways, skills
📁 03-utilisation/
   ├── quotidien.md        → Usage quotidien (comme LEO)
   ├── crons.md            → Tâches planifiées
   └── dashboards.md       → Monitoring et KPIs
📁 04-aller-plus-loin/
   └── troubleshooting.md  → Problèmes courants et solutions
📁 exemples/
   └── LEO.md              → L'architecture complète de LEO
📁 references/
   └── commandes.md        → Aide-mémoire des commandes
```

## Public visé

- **Débutant·e** — les pages d'installation vous guident pas à pas
- **Intermédiaire** — la configuration et les skills vous ouvrent les possibilités
- **Expert·e** — le troubleshooting et l'architecture LEO vous inspirent

## Licence

Ce guide est en licence libre — libre à vous de le partager, l'adapter et l'enrichir.

---

**Projet vivant** — ce guide évolue avec Hermes Agent. N'hésitez pas à contribuer !
