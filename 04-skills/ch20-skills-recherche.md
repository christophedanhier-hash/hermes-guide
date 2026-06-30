# Skills recherche et veille : AI Tech Watch, arxiv

Hermes peut surveiller l'actualité, chercher des articles scientifiques et produire des rapports de veille automatiques. C'est ce que fait LEO chaque matin.

## AI Tech Watch : la veille IA quotidienne

Le skill `ai-tech-watch` est le plus sophistiqué des skills de veille. Chaque matin, il collecte, analyse et résume l'actualité IA.

### Fonctionnement

```
06:00 — Collecte RSS (15 sources)
   │
   ▼
07:00 — Analyse DeepSeek (Phase 1)
   │
   ▼
08:00 — Synthèse + Email (Phase 2)
```

### Sources surveillées

```yaml
# 15 sources RSS
- The Verge (AI)
- TechCrunch (AI)
- Ars Technica (AI)
- Hugging Face Blog
- Google AI Blog
- DeepMind Blog
- Meta AI Blog
- Anthropic Blog
- OpenAI Blog
- Microsoft Research (AI)
- NVIDIA Blog (AI)
- MIT News (AI)
- Stanford AI (HAI)
- Papers With Code
- Arxiv (AI)
```

### Format du rapport

Chaque rapport suit le format "Cowork Copilote" :

```markdown
# 🧠 Veille IA — 28/06/2026

Ce matin dans l'IA : [résumé 2-3 lignes des tendances clés]

## 🤖 IA Générale
- **Titre article** (source) — ALERTE
  Analyse : Contexte, annonce, signification, impact DSI...

## 🔒 Cyber
- ...

## ☁️ Cloud & Infra
- ...

## 📋 Régulation
- ...
```

### Tags utilisés

| Tag | Signification |
|:----|:--------------|
| 🔴 ALERTE | Information majeure, action possible |
| 🟡 NOUVEAU | Nouveauté intéressante |
| 🟢 À SUIVRE | Tendances émergentes |
| 🔵 CONFORMITÉ | Impact réglementaire |
| 🟣 MENACE | Risque sécurité/compétitivité |
| ⚪ PATCH | Mise à jour corrective |
| 🔶 TENDANCE | Signal faible |

### Coût

```yaml
Coût quotidien: ~0,05 € (DeepSeek Flash)
Nombre d'articles analysés: 15-30
Temps de traitement: ~2 minutes
```

## Arxiv : la recherche académique

Le skill `arxiv` permet de chercher des articles scientifiques sur Arxiv.

### Recherche par mot-clé

```bash
python3 /opt/data/scripts/search_arxiv.py --query "hermes agent LLM" --max 5
```

### Recherche par auteur

```bash
python3 /opt/data/scripts/search_arxiv.py --author "danhier" --max 10
```

### Recherche par catégorie

```bash
python3 /opt/data/scripts/search_arxiv.py --category cs.AI --sort date --max 20
```

### Format des résultats

```markdown
| Titre | Auteurs | Date | Catégorie | Lien |
|:------|:--------|:----:|:----------|:----|
| Titre de l'article | Auteur 1, Auteur 2 | 2026-06 | cs.AI | [PDF](url) |
```

## BlogWatcher : surveiller les blogs

Le skill `blogwatcher` surveille les flux RSS et Atom des blogs techniques.

### Ajouter une source

```bash
blogwatcher add https://blog.example.com/feed.xml
```

### Lister les sources

```bash
blogwatcher list
```

### Voir les nouveaux articles

```bash
blogwatcher recent --days 1
```

## LLM Wiki : base de connaissance locale

Le skill `llm-wiki` crée une base de connaissance interrogeable à partir de fichiers Markdown.

```bash
# Indexer le wiki
python3 /opt/data/scripts/index_wiki.py /opt/data/BAVI_LEO/docs/

# Interroger
python3 /opt/data/scripts/query_wiki.py "Comment configurer un gateway ?"
```

Utile pour que Hermes puisse chercher dans sa propre documentation sans avoir tout en contexte.

## Polymarket : les marchés de prédiction

Le skill `polymarket` interroge les marchés de prédiction Polymarket (blockchain).

```bash
# Derniers marchés
python3 /opt/data/scripts/polymarket.py --trending

# Prix d'un marché spécifique
python3 /opt/data/scripts/polymarket.py --slug "will-agi-exist-by-2030"
```

## YouTube : analyser des vidéos

Le skill `youtube-content` extrait et analyse les transcripts de vidéos YouTube.

```bash
# Extraire le transcript
python3 /opt/data/scripts/fetch_transcript.py https://youtu.be/VIDEO_ID

# Analyser le contenu
# → Résumé, points clés, transcript complet
```

Utile pour les conférences techniques, les tutoriels ou les annonces de produits.

## En résumé

| Skill | Usage | Fréquence | Coût |
|:------|:------|:---------:|:----:|
| **AI Tech Watch** | Veille IA quotidienne | Quotidien | ~0,05 €/j |
| **Arxiv** | Recherche académique | À la demande | 0€ |
| **BlogWatcher** | Surveillance blogs | Continue | 0€ |
| **LLM Wiki** | Base de connaissance | Continue | 0€ |
| **Polymarket** | Marchés prédiction | À la demande | 0€ |
| **YouTube** | Analyse vidéos | À la demande | 0€ |

## Voir aussi

- **Ch.17** : Skills productivité (email, wikis)
- **Ch.26** : Crons — tâches planifiées
- **Annexe A** : Glossaire
