# Chapitre 1 — Un agent IA, c'est quoi ?

> *La différence entre un chatbot et un assistant qui agit*

---

Si vous lisez ce livre, vous avez probablement déjà utilisé ChatGPT, Claude, ou Gemini. Vous tapez une question, l'IA répond. Simple, efficace.

Mais vous avez aussi ressenti la frustration : « *Peux-tu envoyer cet email à Paul ?* » → « Je suis désolé, je ne peux pas envoyer d'email. » C'est là que la différence entre **chatbot** et **agent** devient flagrante.

## Chatbot vs Agent : la différence fondamentale

```
┌──────────────────────────────────────────────────────┐
│                      CHATBOT                         │
│  Vous : "Quel temps fait-il à Paris ?"               │
│  Bot  : "Il fait 22°C et ciel dégagé."              │
│  ───────────────────────────────────────────────────  │
│  📢 Il PARLE — mais n'AGIT PAS                       │
└──────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────┐
│                      AGENT                           │
│  Vous : "Prépare un email pour Paul avec le          │
│          compte-rendu de la réunion d'hier."         │
│  Agent : [Cherche le document]                       │
│          [Rédige le résumé]                          │
│          [Ouvre Gmail]                               │
│          [Envoie l'email]                            │
│         "✅ Email envoyé à Paul avec le CR."          │
│  ───────────────────────────────────────────────────  │
│  🦁 Il PARLE ET AGIT                                 │
└──────────────────────────────────────────────────────┘
```

Un **agent IA**, c'est un chatbot qui a :

| Capacité | Chatbot | Agent |
|:---------|:-------:|:-----:|
| Répondre à des questions | ✅ | ✅ |
| Exécuter des commandes sur votre système | ❌ | ✅ |
| Lire et écrire des fichiers | ❌ | ✅ |
| Envoyer des emails | ❌ | ✅ |
| Planifier des actions dans le futur | ❌ | ✅ |
| Utiliser des outils (calculatrice, API, navigateur) | ❌ | ✅ |
| Apprendre de ses erreurs entre sessions | ❌ | ✅ |
| Travailler sans supervision | ❌ | ✅ |

## Ce que LEO fait que ChatGPT ne peut pas faire

LEO, c'est l'assistant de Christophe — et il n'est pas juste « une interface vers DeepSeek ». Voici ce qu'il fait tous les jours sans intervention humaine :

| À 06:00 | Backup automatique des fichiers critiques |
|:--------|:------------------------------------------|
| À 06:05 | Relevé du solde DeepSeek → écrit dans Google Sheets |
| À 06:10 | Met à jour le dashboard KPI |
| À 06:15 | Collecte CPU/RAM/disque des 3 machines |
| À 06:20 | Vérifie que tous les crons tournent bien |
| À 06:25 | Met à jour l'activité GitHub |
| Toutes les 15 min | Classifie les emails entrants |
| Toutes les 30 min | Synchronise sa mémoire entre profils |
| À 08:00 | Analyse 17 sources RSS → envoie la veille IA par email |
| À 18:00 | Synchronise Drive ↔ GitHub |

Et tout ça **sans que Christophe ait à lui demander**. LEO anticipe.

## Les briques d'un agent IA

Derrière la magie, un agent comme Hermès est construit sur cinq piliers :

### 1. 🧠 Le modèle (LLM)
Le cerveau. DeepSeek, Gemini, Ollama — c'est ce qui comprend votre demande et décide quoi faire. L'avantage d'Hermès ? Vous n'êtes pas enfermé chez un seul fournisseur : vous pouvez basculer de DeepSeek à Ollama (gratuit, local) en une commande.

### 2. 🔧 Les outils
Là où le chatbot s'arrête (« je ne peux pas faire ça »), l'agent commence. Hermès dispose de tout un arsenal d'outils :

- **terminal** — exécute des commandes shell
- **read_file / write_file** — lit et écrit des fichiers
- **web** — cherche sur Internet
- **browser** — navigue comme un humain
- **cronjob** — planifie des actions
- **delegate_task** — délègue à des sous-agents

### 3. 🧬 La mémoire
Un chatbot oublie tout entre deux conversations. Un agent se souvient :
- De votre nom, vos préférences, votre environnement
- Des leçons apprises (« ne pas envoyer deux fois le même email »)
- Des procédures qui ont fonctionné

### 4. 🏃 Les actions planifiées
Un agent ne se contente pas de répondre — il agit dans le futur. Les **crons** sont le secret pour qu'un assistant devienne un véritable majordome numérique : il fait le café avant que vous ne vous réveilliez.

### 5. 🎓 Les skills
C'est le secret le mieux gardé d'Hermès. Un **skill**, c'est un mode d'emploi que vous donnez à l'agent pour qu'il sache comment faire quelque chose. « Voici comment déployer un dashboard. Voici comment envoyer un email. Voici comment analyser un RSS. » Une fois écrit, ce savoir est réutilisable à l'infini.

## L'erreur à ne pas commettre

> « Je vais mettre tous mes modèles et outils dans le même agent et ça va marcher. »

**Non.** Un agent sans organisation, c'est le chaos. LEO a appris cette leçon à ses dépens (on vous racontera ça au chapitre 3). La clé, c'est la **structuration** :

- Des profils séparés pour des tâches différentes
- Des crons avec des horaires staggerés (décalés)
- Des skills qui encapsulent le savoir-faire
- Des bureaux qui organisent les connaissances

## 📝 À retenir

| Concept | À retenir |
|:--------|:----------|
| **Agent ≠ Chatbot** | Un chatbot répond. Un agent agit. |
| **LEO est un agent** | Il exécute des actions, pas seulement des réponses. |
| **5 piliers** | Modèle, outils, mémoire, crons, skills. |
| **Organisation** | Sans structure, un agent devient incontrôlable. |

---

**[Chapitre suivant → Pourquoi Hermès ?](ch02-pourquoi-hermes.md)**
