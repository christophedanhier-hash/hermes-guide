# Installation sur Linux (Debian/Ubuntu)

## 1. Prérequis

- Un serveur ou PC sous **Debian 12+** ou **Ubuntu 22.04+**
- **Python 3.11+**
- **Curl**
- Un compte GitHub
- (Optionnel) Un GPU NVIDIA pour exécuter des LLM locaux

## 2. Installer Hermes Agent (méthode officielle)

```bash
# Méthode recommandée — script d'installation automatique
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
```

Ce script :
- Détecte votre OS et architecture
- Installe les dépendances système nécessaires
- Télécharge la dernière version d'Hermes
- Crée un environnement virtuel isolé
- Vous guide dans la configuration initiale

### Installation manuelle (alternative)

Si vous préférez une installation manuelle ou que le script ne fonctionne pas :

```bash
# Cloner le dépôt
git clone https://github.com/NousResearch/hermes-agent.git
cd hermes-agent

# Créer l'environnement virtuel
python3 -m venv .venv
source .venv/bin/activate

# Installer Hermes
pip install -e .
```

## 3. Configurer Hermes

```bash
# Assistant de configuration interactif
hermes setup

# Vérifier que tout est OK
hermes doctor
```

Le wizard de configuration vous guidera pour :
- Choisir votre fournisseur LLM principal (`hermes model`)
- Configurer votre profil Telegram (optionnel)
- Créer votre profil par défaut

## 4. Connecter un LLM

### Avec DeepSeek (recommandé pour débuter)

```bash
# Ajouter votre clé DeepSeek dans .env
echo "DEEPSEEK_API_KEY=votre_clé_ici" >> ~/.hermes/.env

# Configurer le provider DeepSeek
hermes config set model.default deepseek-chat
hermes config set model.provider deepseek
```

### Avec Ollama (local, gratuit)

```bash
# Installer un modèle
ollama pull qwen2.5:7b

# Configurer Hermes pour utiliser Ollama
hermes config set model.default qwen2.5:7b
hermes config set model.provider ollama
hermes config set model.base_url "http://localhost:11434/v1"
```

## 5. Lancer Hermes

```bash
# En mode interactif (terminal)
hermes
# ou : hermes chat

# En mode one-shot (requête unique)
hermes chat -q "Dis bonjour, je suis ton nouvel assistant"

# Avec Telegram (après configuration du bot)
hermes gateway run
# ou en service :
hermes gateway install
hermes gateway start
```

## 6. Vérifier l'installation

```bash
# Test simple
hermes chat -q "Dis bonjour, je suis ton nouvel assistant"

# Diagnostiquer les problèmes
hermes doctor --fix

# Voir l'état des composants
hermes status
```

## Problèmes courants

| Problème | Solution |
|----------|----------|
| `ModuleNotFoundError: No module named '...'` | Vérifier que le venv est activé |
| `pip` introuvable | `sudo apt install python3-pip` |
| Erreur de permission | Utiliser un utilisateur normal (pas root) |
| Port déjà utilisé | Modifier le port dans `config.yaml` |

## Ressources

- [Documentation officielle Hermes](https://hermes-agent.nousresearch.com/docs)
- [GitHub Hermes Agent](https://github.com/NousResearch/hermes-agent)
