# Drive ↔ GitHub sync

La synchronisation entre Google Drive et GitHub est le pont qui relie les documents collaboratifs à la documentation publiée.

## Pourquoi synchroniser ?

```yaml
Google Drive:
  - Écriture collaborative (Google Docs)
  - Accessible depuis n'importe quel appareil
  - Versionnage basique

GitHub Pages:
  - Publication publique et élégante
  - Versionnage professionnel (git)
  - Déploiement automatique (MkDocs)

Le sync = le meilleur des deux mondes
```

## Fonctionnement

```
Google Docs
     │
     ▼
Drive Watch (toutes les 6h)
     │
     ▼
Conversion .docx → .md
     │
     ▼
Git add + commit + push
     │
     ▼
GitHub Pages (déploiement automatique)
     │
     ▼
Wiki en ligne 🌐
```

## Script de synchronisation

```bash
#!/bin/bash
# drive-sync.sh

# 1. Scanne les dossiers Drive partagés
python3 /opt/data/scripts/drive-sync.py --scan

# 2. Détecte les nouveaux fichiers
python3 /opt/data/scripts/drive-sync.py --diff

# 3. Convertit les .docx en .md
python3 /opt/data/scripts/drive-sync.py --convert

# 4. Commit + push
cd /opt/data/BAVI_LEO && git add -A && git commit -m "sync Drive $(date +%Y-%m-%d)" && git push
cd /opt/data/voyages-wiki && git add -A && git commit -m "sync Drive $(date +%Y-%m-%d)" && git push
cd /opt/data/emile-wiki && git add -A && git commit -m "sync Drive $(date +%Y-%m-%d)" && git push
```

## Wikis synchronisés

| Wiki | Dossier Drive | Usage |
|:-----|:--------------|:------|
| **BAVI_LEO** | `Hermes_Christophe/BAVI/` | Documentation bureaux |
| **voyages-wiki** | `Hermes_Christophe/Voyages/` | Roadbooks camping-car |
| **emile-wiki** | `bavi/bureau-emile/` | Mémoire universitaire |

## Résolution de conflits

```yaml
Règle: GitHub gagne en cas de conflit.
  - Drive est la source des nouveaux documents
  - GitHub est la source de vérité pour les modifications existantes
  - En cas de modification simultanée : version GitHub prioritaire
```

## Voir aussi

- **Ch.12** : Bureau Sylvia (publication des roadbooks)
- **Ch.13** : Bureau Émile (sync des brouillons)
- **Ch.17** : Skills productivité
