# Chapitre 4 — Installation rapide

> *Un assistant IA opérationnel en 5 minutes chrono*

---

Assez de théorie. Installons Hermès et faisons notre premier test.

## Prérequis

- **Linux** (Debian/Ubuntu recommandé), **macOS**, ou **Windows avec WSL2**
- **curl** installé (99% des systèmes l'ont)
- **Git** (optionnel mais recommandé)
- 1 Go d'espace disque libre

## Installation (Linux / macOS)

Une seule commande :

```bash
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
```

L'installateur détecte automatiquement votre système, installe les dépendances, et ajoute Hermès à votre PATH.

**Que se passe-t-il en coulisses :**
1. Téléchargement de la dernière version
2. Création d'un environnement virtuel Python
3. Installation des dépendances
4. Configuration minimale automatique

Après installation, fermez et rouvrez votre terminal, ou :

```bash
source ~/.bashrc
```

## Premier lancement

```bash
# Lancer Hermès en mode interactif
hermes
```

Vous devriez voir apparaître l'interface :

```
┌─────────────────────────────────────────────────────────┐
│  ⚕ Hermes Agent v0.17.0                                │
│  Type /help for slash commands, /quit to exit            │
└─────────────────────────────────────────────────────────┘

You >
```

Félicitations 🎉 — votre premier agent IA tourne !

### Test rapide

```bash
hermes chat -q "Hello, qui es-tu ?"
```

Hermès vous répond. Simple.

## Configuration minimale

Par défaut, Hermès n'a pas de clé API LLM — il vous demandera d'en configurer une au premier lancement. Vous avez plusieurs options :

### Option A : DeepSeek (recommandé pour débuter)

Créez un compte sur [platform.deepseek.com](https://platform.deepseek.com) et récupérez votre clé API. Ajoutez-la ensuite :

```bash
hermes config set model.default deepseek-v4-flash
hermes config set model.provider deepseek
```

Puis éditez le fichier `.env` :

```bash
hermes config env-path
# Ajoutez : DEEPSEEK_API_KEY=votre_clé_ici
```

### Option B : Ollama (100% gratuit, local)

Si vous avez un GPU ou un CPU récent :

```bash
# Installer Ollama
curl -fsSL https://ollama.com/install.sh | sh

# Télécharger un modèle
ollama pull qwen2.5:7b  # 4 Go, bon rapport qualité/ressources

# Configurer Hermes
hermes config set model.default qwen2.5:7b
hermes config set model.provider ollama
```

Pas de clé API, pas de compte, tout tourne chez vous.

### Option C : Gemini (gratuit, cloud)

```bash
# Obtenez une clé sur https://aistudio.google.com/apikey
export GEMINI_API_KEY=votre_clé_ici

# Configuration
hermes config set model.default gemini-2.5-flash
```

## Vérification de l'installation

```bash
hermes doctor
```

Cette commande vérifie :
- ✅ Que les dépendances sont installées
- ✅ Que la configuration est valide
- ✅ Que les clés API sont accessibles
- ✅ Que les outils système sont disponibles

Un rapport s'affiche. Si tout est vert, vous êtes prêt.

## Et ensuite ?

Vous avez un agent IA de base. Mais un agent seul dans son terminal, c'est un peu comme un smartphone sans applications. La vraie puissance arrive quand vous :

1. **Connectez Telegram** (Chapitre 5) — pour parler à votre agent depuis votre téléphone
2. **Ajoutez des skills** (Chapitre 8) — pour qu'il sache faire des choses
3. **Configurez des providers** (Chapitre 6) — pour optimiser coût et performance
4. **Planifiez des tâches** (Chapitre 26) — pour qu'il travaille sans vous

### ⚠️ Piège à éviter

> Ne pas tout vouloir configurer le premier jour.

Commencez avec un seul provider (DeepSeek ou Ollama). Ajoutez Telegram une fois que l'agent répond correctement en ligne de commande. Ajoutez les skills un par un. La précipitation est l'ennemie de la fiabilité — LEO en a fait l'expérience.

Si vous rencontrez des problèmes :

```bash
# Voir les logs
hermes --verbose

# Réinstaller proprement
hermes uninstall
# Réinstaller
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
```

## 📝 À retenir

| Étape | Commande |
|:------|:---------|
| Installer | `curl -fsSL https://hermes-agent.nousresearch.com/install.sh \| bash` |
| Lancer | `hermes` |
| Tester | `hermes chat -q "Bonjour"` |
| Diagnostiquer | `hermes doctor` |
| Configurer provider | `hermes model` |

---

**[Partie II → Configurer son assistant](../02-configurer/ch05-gateway.md)**

> **💡 Astuce :** Vous voulez voir un exemple concret ? Passez directement à [l'architecture complète de LEO](../annexes/exemple-leo-complet.md) en annexe.
