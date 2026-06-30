# Mémoire persistante et profils utilisateur

Un assistant qui ne se souvient de rien est inutile. Hermes Agent propose un système de mémoire persistante qui permet à votre bot de **se souvenir de vous entre les sessions**, même après un redémarrage.

## Les deux types de mémoire

### 1. MEMORY.md — La mémoire du système

C'est le carnet de bord de l'assistant. Il y note tout ce qui concerne l'infrastructure, les configurations, les procédures et les décisions.

```markdown
# Contenu typique
Infrastructure: serveur Ubuntu 26.04, 457Go SSD, Docker + n8n + ollama
§
Backup: quotidien vers GDrive à 04:00, rétention 7 jours
§
Budget DeepSeek: $60.31 restant, ~$0.03/jour
§
WiFi camping: "Camping Le Brasilia" — mot de passe en vault
```

### 2. USER.md — Le profil utilisateur

Contient tout ce que l'assistant sait sur vous : vos préférences, votre style, vos proches, vos habitudes.

```markdown
Christophe (Tofdan) — conseiller stratégique IT, Sombreffe, Belgique 🇧🇪
Bilingue français/néerlandais. Fuseau Europe/Brussels.
Femme: Sylvie. Filles: Émilie (30) et Camille (26) + Célestine 🎀
Chienne: Nala 🐶
§
Style: direct, action-first, zéro blabla. Exige vérification avant livraison.
```

## Comment ça marche

### Écriture automatique

Le système de mémoire s'active automatiquement :

```yaml
# Dans config.yaml
memory:
  memory_enabled: true          # Activer la mémoire
  user_profile_enabled: true     # Activer le profil utilisateur
  write_approval: false          # Écrire sans demander
  memory_char_limit: 2200        # Taille max de MEMORY.md
  user_char_limit: 1375          # Taille max de USER.md
  nudge_interval: 10             # Toutes les 10 interactions, demander si mise à jour
  flush_min_turns: 6             # Synchroniser tous les 6 tours
```

À chaque interaction, Hermes décide si une information est suffisamment importante pour être mémorisée.

### Emplacement des fichiers

```bash
~/.hermes/memories/
├── MEMORY.md        # Mémoire système
└── USER.md          # Profil utilisateur
```

Chaque profil Hermes a ses propres fichiers de mémoire.

## Partage de mémoire entre profils

Dans l'écosystème LEO, les profils peuvent **partager la même mémoire** via des liens symboliques :

```bash
# Créer un lien symbolique pour partager la mémoire
ln -s /opt/data/memories/MEMORY.md /opt/data/profiles/leo-copilot/memories/MEMORY.md
ln -s /opt/data/memories/USER.md /opt/data/profiles/leo-copilot/memories/USER.md
```

Avantage : quand un bot apprend quelque chose, l'autre le sait aussi immédiatement.

```
LEO (default) écrit ──→ /opt/data/memories/MEMORY.md
                              ↕ symlink
Léo Copilote lit ──→ /opt/data/profiles/leo-copilot/memories/MEMORY.md
                              (même fichier !)
```

## Cas pratique : la mémoire de LEO

### MEMORY.md (extrait réel)

```
RÈGLE commit : toute modif fichier repo git = commit + push immédiat
§  
Wikis: BAVI_LEO=portail, hermes-christophe=source, les2→sync+push
§
hermes binaire: /opt/hermes/.venv/bin/hermes (pas sur PATH)
§
CRASH+RECONSTRUCTION 30/06: sessions vidé→4 bots crash. 
Backup GDrive 73.7MB téléchargé + extrait. 4 gateways relancés.
§
Émile 🎓: emidanhier@gmail.com, @Bureau_ia_emilie_bot
```

### USER.md (extrait réel)

```
Christophe: Décisif, action directe, pattern-first.
Exige vérification AVANT livraison (curl 200, grep valeur réelle).
Zéro tolérance oubli sync (BAVI_LEO + hermes-christophe).
Préfère process automatisé aux corrections.
```

## La synchronicité cross-session

La mémoire traverse les sessions. Si vous dites "souviens-toi que mon serveur est à Bruxelles", l'information persiste :

```
Session 1 : "Mon serveur est à Bruxelles"
  → Hermes écrit dans USER.md : "Serveur situé à Bruxelles (Eвропа/Brussels)"

Session 2 (le lendemain) : "Quelle est l'IP de mon serveur ?"
  → Hermes lit USER.md : "Serveur situé à Bruxelles..."
  → Peut répondre sans avoir à redemander
```

## Limites et bonnes pratiques

| Limite | Explication |
|:-------|:------------|
| **Taille max** | 2 200 caractères pour MEMORY.md, 1 375 pour USER.md |
| **Pas de recherches** | La mémoire est injectée en entier. Trop d'informations → contexte dilué |
| **Pas de structure** | Format libre, pas de base de données |
| **Un seul fichier** | Pas de sous-dossiers, pas de clés-valeurs |

### Conseils pour une mémoire efficace

1. **Priorisez ce qui est stable** : les préférences, l'infrastructure, les règles
2. **N'écrivez pas l'évident** : inutile de noter "le ciel est bleu"
3. **Consolidez régulièrement** : fusionnez les anciennes entrées, supprimez les obsolètes
4. **Séparez les faits des tâches** : les procédures vont dans les skills, pas dans la mémoire
5. **Utilisez les sauts de section** (`§`) pour séparer les sujets

## Voir aussi

- **Ch.8** : les skills (pour les procédures réutilisables)
- **Ch.3** : l'architecture LEO (comment s'organisent les profils)
- **Annexe A** : glossaire (mémoire persistante)
