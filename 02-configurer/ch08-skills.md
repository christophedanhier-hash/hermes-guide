# Skills : le super-pouvoir d'Hermès

Un skill Hermes est un fichier Markdown qui décrit **comment accomplir une tâche spécifique**. C'est le mécanisme qui transforme un assistant généraliste en expert multi-domaines.

## Principe

```
Skill = fichier .md + dossier
├── SKILL.md          ← Les instructions : contexte, étapes, pièges
├── references/       ← Documentation complémentaire
├── templates/        ← Modèles de sortie
└── scripts/          ← Scripts exécutables
```

Quand vous posez une question, Hermes charge les skills pertinents dans son contexte et les utilise comme guide d'exécution.

### Exemple : un skill "Installation Nginx"

```markdown
---
name: nginx-install
description: Installer et configurer Nginx avec Cloudflare Tunnel
category: infrastructure
---

# Installation Nginx + Cloudflare

## Étapes
1. Installer Nginx : `apt install nginx -y`
2. Copier la configuration : `/etc/nginx/sites-available/tofdan.be`
3. Activer le site : `ln -s ... /etc/nginx/sites-enabled/`
4. Tester : `nginx -t`
5. Recharger : `systemctl reload nginx`

## Pièges
- Ne pas ouvrir le port 443 (Cloudflare gère HTTPS)
- Vérifier que le tunnel Cloudflare est actif avant
```

## L'écosystème de skills LEO

Environ **117 skills** répartis en **22 catégories** :

```
skills/
├── infrastructure/     ← Docker, Nginx, Cloudflare, backup
│   └── bureau-michel/SKILL.md
├── github/             ← PRs, issues, code review, auth
├── creative/           ← ASCII art, design, vidéo, musique
├── data-science/       ← Jupyter, analyse de données
├── email/              ← Gmail, classification inbox zero
├── productivity/       ← Dashboards, wikis, OCR, PowerPoint
├── research/           ← Arxiv, veille IA, blogwatcher
├── media/              ← GIF, YouTube, audio
├── software-dev/       ← TDD, debug, code review
├── smart-home/         ← Philips Hue (openhue)
├── note-taking/        ← Obsidian
├── mlops/              ← LLM eval, vLLM, HuggingFace
└── 10+ autres...
```

### Skills système vs skills customs

**Skills système** (intégrés à Hermes Agent) :
- `hermes-agent` : configurer Hermes lui-même
- `computer-use` : piloter un bureau à distance
- `plan` : planifier des tâches complexes

**Skills customs** (écrits par vous ou la communauté) :
- `bureau-michel` : infrastructure n8n et déploiement
- `voyages-wiki` : publication de roadbooks
- `gmail-inbox-zero` : classification automatique des emails

### Comment Hermes charge les skills

```
1. Vous dites : "Installe Nginx sur le serveur"
2. Hermes cherche des skills pertinents
3. Trouve "infrastructure/nginx-install"
4. Charge le SKILL.md dans son contexte
5. Exécute les étapes décrites
6. Applique les pièges et vérifications
```

## Créer son premier skill

### 1. Structure minimale

```markdown
---
name: mon-premier-skill
description: Un exemple simple
---

# Mon premier skill

Fait ceci, puis cela, puis vérifie.
```

### 2. Avec frontmatter complet

```yaml
---
name: backup-gdrive
description: Backup des profils Hermes vers Google Drive
category: infrastructure
metadata:
  hermes:
    tags: [backup, gdrive, hermes]
---
```

### 3. Avec sous-dossiers

```bash
mon-skill/
├── SKILL.md
├── references/
│   └── api-endpoints.md
├── templates/
│   └── rapport.md
└── scripts/
    └── backup.py
```

Les scripts dans `scripts/` peuvent être appelés directement par le skill.

### 4. Règles d'or

1. **Frontmatter obligatoire** : `name`, `description`, `category`
2. **Un skill = une tâche** : pas de fourre-tout
3. **Pitfalls** : documentez ce qui peut mal tourner
4. **Vérification** : incluez une étape de validation
5. **Réutilisable** : écrivez pour que n'importe qui (ou vous-même dans 3 mois) puisse l'exécuter

## Skills et profils : la source de vérité

Dans l'écosystème LEO, le profil `default` (LEO) est la **source de vérité** des skills. Les autres profils (Léo Copilote, Sylvia, Émile) reçoivent les skills par **synchronisation automatique** toutes les 30 minutes.

```
default (source) ──sync 30min──→ leo-copilot
                ──sync 30min──→ bavi-leo (Sylvia)
                ──sync 30min──→ emile
```

Avantage : vous mettez à jour un skill une fois, et tous les bots en bénéficient.

## Skills préinstallés utiles

| Skill | Catégorie | Utilité |
|:------|:----------|:--------|
| `hermes-agent` | Système | Configurer Hermes lui-même |
| `github-pr-workflow` | GitHub | Créer et gérer des PRs |
| `gmail-inbox-zero` | Email | Classifier ses emails |
| `plan` | Dev | Planifier un projet complexe |
| `test-driven-development` | Dev | Coder en TDD |
| `youtube-content` | Media | Analyser des vidéos YouTube |
| `obsidian` | Notes | Lire/écrire dans Obsidian |
| `open-hue` | Maison | Contrôler les lumières Philips Hue |

## Gestion des skills avec le curator

Hermes inclut un **curator** qui nettoie automatiquement les skills :
- Skills inactifs > 30 jours → marqués comme "stale"
- Skills inactifs > 90 jours → archivés
- Skills obsolètes → supprimés

```yaml
# Activation dans config.yaml
curator:
  enabled: true
  interval_hours: 168     # Une fois par semaine
  stale_after_days: 30
  archive_after_days: 90
  prune_builtins: true     # Nettoie aussi les skills système inutilisés
```

## En résumé

| Concept | À retenir |
|:--------|:----------|
| **Skill** | Fichier .md qui décrit une tâche |
| **Catégorie** | Groupe de skills (infra, github, creative...) |
| **Source de vérité** | Un profil central push vers les autres |
| **Curator** | Nettoyage automatique des skills obsolètes |
| **Scripts** | Code exécutable dans `scripts/` |
| **Pitfalls** | Pièges documentés pour éviter les erreurs |
