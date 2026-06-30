# Crons quotidiens : backup, veille IA, sync

Les crons quotidiens sont les tâches lourdes qui s'exécutent une fois par jour. Backup, veille IA, synchronisation — le ménage automatisé.

## Les crons quotidiens de LEO

```yaml
04:00 — Backup → GDrive
  Action: Archive tous les profils + config → Google Drive
  Rétention: 7 jours
  Coût: 0 € (no_agent)
  Script: /opt/data/scripts/hermes-backup.py

07:00 — Veille IA
  Action: Collecte 15 sources RSS → analyse DeepSeek → email
  Coût: ~0,05 €/jour (agent LLM)
  Durée: ~2 minutes

09:00 — Hermes Update Check
  Action: Vérifie si une nouvelle version d'Hermes est disponible
  Coût: 0 € (no_agent)

18:00 — Sync Drive → GitHub
  Action: Miroir bidirectionnel Google Drive ↔ GitHub
  Coût: 0 € (no_agent)

23:00 — Budget Snapshot
  Action: Sauvegarde du budget quotidien dans l'historique
  Coût: 0 € (no_agent)
```

## Backup quotidien

```yaml
Ce qui est sauvegardé:
  - profiles/default/       → config, SOUL, .env
  - profiles/leo-copilot/   → config, crons, mémoire, skills
  - profiles/bavi-leo/      → config, mémoire, roadbooks
  - profiles/emile/         → config, SOUL, .env
  - memories/               → MEMORY.md, USER.md (partagés)
  - scripts/                → tous les scripts customs
  - sessions/               → historique des conversations
  - .env                    → tokens et clés API
  - config.yaml             → configuration globale
  - credentials_vault.json  → coffre-fort des credentials

Destination:
  - Local: /opt/data/backups/
  - Cloud: Google Drive (Hermes_Christophe/backups/)
  
Rétention: 7 jours
Taille moyenne: ~40-70 MB
```

## Veille IA quotidienne

```yaml
Processus:
  1. Collecte RSS (15 sources, ~50 articles)
  2. DeepSeek V4 Flash analyse chaque article
  3. Sélection des 15 plus pertinents
  4. Rédaction du rapport formaté
  5. Envoi par email à christophe.danhier@gmail.com

Coût: ~0,05 €/jour = ~1,50 €/mois
Tags: ALERTE, NOUVEAU, À SUIVRE, CONFORMITÉ, TENDANCE
```

## Synchronisation Drive → GitHub

```yaml
Fonctionnement:
  - Scanne les dossiers Google Drive partagés
  - Détecte les nouveaux fichiers .docx ou .md
  - Convertit les .docx en .md
  - Commit + push sur le wiki GitHub correspondant

Wikis synchronisés:
  - BAVI_LEO ↔ Drive (docs bureaux)
  - voyages-wiki ↔ Drive (roadbooks)
  - emile-wiki ↔ Drive (brouillons mémoire)
```

## Planification avec cron

```yaml
# Format: minute heure jour mois jour_semaine
0 4 * * *   → Tous les jours à 04:00
0 7 * * *   → Tous les jours à 07:00
0 9 * * *   → Tous les jours à 09:00
0 18 * * *  → Tous les jours à 18:00
0 23 * * *  → Tous les jours à 23:00
```
