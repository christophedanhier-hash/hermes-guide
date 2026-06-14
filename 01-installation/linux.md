# Installation sur Linux (Debian/Ubuntu)

## 1. Prérequis

- Un serveur ou PC sous **Debian 12+** ou **Ubuntu 22.04+**
- **Python 3.11+**
- **Git**
- **Curl**
- Un compte GitHub
- (Optionnel) Un GPU NVIDIA pour exécuter des LLM locaux

## 2. Installer les dépendances

```bash
# Mise à jour des paquets
sudo apt update && sudo apt upgrade -y

# Dépendances Python
sudo apt install -y python3 python3-venv python3-pip git curl

# (Optionnel) Pour Ollama local
curl -fsSL https://ollama.com/install.sh | sh
```

## 3. Installer Hermes Agent

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

## 4. Configurer Hermes

```bash
# Initialiser la configuration
hermes setup

# Vérifier que tout est OK
hermes doctor
```

Le wizard de configuration vous guidera pour :
- Choisir votre fournisseur LLM principal
- Configurer votre profil Telegram (optionnel)
- Créer votre profil par défaut

## 5. Connecter un LLM

### Avec DeepSeek (recommandé pour débuter)

```bash
# Ajouter votre clé DeepSeek
hermes config set DEEPSEEK_API_KEY "sk-votre-cle-ici"

# Configurer le provider DeepSeek
hermes config set model.default deepseek-chat
hermes config set model.provider deepseek
```

### Avec Ollama (local, gratuit)

```bash
# Installer un modèle
ollama pull qwen2.5:7b

# Configurer Hermes pour utiliser Ollama
hermes config set providers.custom.ollama.base_url "http://localhost:11434/v1"
hermes config set providers.custom.ollama.api_key "ollama"
```

## 6. Lancer Hermes

```bash
# En mode interactif (terminal)
hermes run

# Avec Telegram (après configuration du bot)
hermes gateway start
```

## 7. Vérifier l'installation

```bash
# Test simple
hermes run -m "Dis bonjour, je suis ton nouvel assistant"
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
