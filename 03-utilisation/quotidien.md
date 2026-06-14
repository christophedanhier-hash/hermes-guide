# Utilisation quotidienne (comme LEO)

Une fois Hermes installé et configuré, voici comment l'utiliser au quotidien.

## Parler à votre assistant

### Depuis Telegram

Une fois le gateway Telegram lancé, ouvrez votre bot et commencez à parler :

```
👤 "LEO, quel est mon solde DeepSeek ?"
🤖 "💰 $42.43 (solde au 14/06/2026)"

👤 "Fais un backup de ma config"
🤖 "✅ 4 fichiers backupés sur Google Drive"
```

### Depuis le terminal

```bash
hermes run
```

Ou en mode one-shot :

```bash
hermes run -m "Résume ce fichier"
```

## Tâches courantes

### Lecture et analyse de documents

Votre assistant peut lire des fichiers texte, PDF, ou extraire des pages web :

```
👤 "Analyse ce PDF et donne-moi un résumé"
👤 "Extrais les informations clés de cette page web"
```

### Gestion de fichiers

```
👤 "Crée un dossier pour le projet X"
👤 "Déplace les fichiers .tmp dans /tmp/"
```

### Envoi d'emails

```
👤 "Envoie un email à Jean-Paul pour confirmer la réunion"
```

**Important :** Votre assistant a besoin d'un accès SMTP/IMAP configuré pour envoyer des emails.

### Planification de tâches (crons)

```
👤 "Vérifie mon solde DeepSeek tous les jours à 7h"
👤 "Fais un backup de ma config tous les matins à 6h"
```

Voir `03-utilisation/crons.md` pour les détails.

## La règle d'or de la communication

Plus vos consignes sont claires, meilleur est le résultat.

### ✅ Bonnes pratiques

| Au lieu de... | Dites... |
|---------------|----------|
| "Fais quelque chose" | "Analyse ce fichier et donne-moi un résumé en 3 points" |
| "Corrige ça" | "Le script backup plante avec 'Broken pipe'. Trouve la cause et corrige-la" |
| "Je veux un rapport" | "Génère un rapport hebdo du budget avec le solde, la tendance et une projection" |

### 🚫 Ce qu'il ne faut pas faire

- **Ne dites pas** "tu dois" ou "tu peux" — votre assistant **fait**, il ne **peut pas** ou **doit**
- **Ne répétez pas** la même consigne — si une correction ne tient pas, demandez à votre assistant de la stocker dans un skill
- **Ne dites pas** "stop" si vous voulez continuer — "stop" est définitif

### ⚡ Votre assistant doit être proactif

Un bon assistant ne devrait pas attendre qu'on lui demande de nettoyer, mettre à jour ou vérifier. Si vous dites :

> *"MAJ la doc"*

Il doit le faire immédiatement, sans expliquer pourquoi c'est important.

## Exemples concrets (avec LEO)

### Budget DeepSeek

```
👤 "LEO, quels sont mes coûts DeepSeek ce mois-ci ?"
```

LEO consulte son dashboard budget et répond avec :
- Solde actuel
- Coût journalier moyen
- Projection jours restants

### État des crons

```
👤 "Tout va bien au niveau des crons ?"
```

LEO consulte son monitoring et répond :
- Nombre de crons OK / erreur
- Dernière exécution
- Prochain run

### Maintenance

```
👤 "Fais le ménage dans les logs"
```

LEO vérifie, supprime ce qui est obsolète, et confirme.

## Pour aller plus loin

- Voir `03-utilisation/crons.md` pour les tâches planifiées
- Voir `03-utilisation/dashboards.md` pour le monitoring
- Voir `exemples/LEO.md` pour l'architecture complète
