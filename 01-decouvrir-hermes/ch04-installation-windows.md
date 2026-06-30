# Installation sur Windows

Hermes Agent fonctionne nativement sous Linux. Sur Windows, la méthode recommandée est **WSL 2** (Windows Subsystem for Linux).

## 1. Installer WSL 2

### 1.1 Activer WSL

Ouvrez **PowerShell en mode Administrateur** et exécutez :

```powershell
# Activer WSL
wsl --install

# Redémarrer l'ordinateur quand demandé
```

Cette commande installe :
- WSL 2
- Une distribution Ubuntu par défaut
- Le noyau Linux

### 1.2 Vérifier l'installation

```powershell
wsl --status
wsl --list --verbose
```

Vous devriez voir une distribution Ubuntu en cours d'exécution avec WSL version 2.

## 2. Installer Python et Git dans WSL

Ouvrez Ubuntu depuis le menu Démarrer ou avec :

```powershell
wsl
```

Dans le terminal Ubuntu :

```bash
# Mise à jour
sudo apt update && sudo apt upgrade -y

# Installer les dépendances
sudo apt install -y python3 python3-venv python3-pip git curl
```

## 3. Installer Hermes Agent

Suivez maintenant les mêmes étapes que pour Linux.

```bash
git clone https://github.com/NousResearch/hermes-agent.git
cd hermes-agent
python3 -m venv .venv
source .venv/bin/activate
pip install -e .
```

## 4. Accéder à Hermes depuis Windows

### Via le terminal WSL

Depuis PowerShell ou CMD :

```powershell
wsl
# Vous êtes maintenant dans Ubuntu
# Activez votre environnement
cd ~/hermes-agent
source .venv/bin/activate
hermes run
```

### Recommandation : utilisez Windows Terminal

[**Windows Terminal**](https://apps.microsoft.com/store/detail/windows-terminal/9N0DX20HK701) est l'application recommandée pour utiliser Hermes sous Windows :

- Onglets multiples (PowerShell + WSL côte à côte)
- Prise en charge complète des couleurs et emojis
- Raccourcis clavier personnalisables

## 5. (Optionnel) Accès aux fichiers Windows depuis WSL

Vos fichiers Windows sont accessibles depuis WSL via :

```bash
# Disque C: monté automatiquement
ls /mnt/c/Users/VotreNom/

# Créer un alias pratique
echo 'alias winhome="cd /mnt/c/Users/VotreNom/"' >> ~/.bashrc
source ~/.bashrc
```

## 6. (Optionnel) Installer Ollama sur Windows

Ollama a une version Windows native :

1. Téléchargez depuis [ollama.com/download](https://ollama.com/download)
2. Installez et lancez Ollama
3. Depuis WSL, Hermes peut accéder à Ollama via l'API :

```bash
# Configurer Hermes pour utiliser Ollama sur Windows
hermes config set providers.custom.ollama.base_url "http://host.docker.internal:11434/v1"
hermes config set providers.custom.ollama.api_key "ollama"
```

L'adresse `host.docker.internal` permet à WSL de communiquer avec les services Windows.

## Différences clés avec Linux

| Aspect | Linux natif | WSL Windows |
|--------|-------------|-------------|
| Performances | Optimal, bare-metal | ~95% (excellentes) |
| GPU pour Ollama | Direct (CUDA/NVIDIA) | Nécessite CUDA dans WSL |
| Démarrage auto | Systemd | Lancement manuel ou tâche planifiée |
| Accès réseau | Direct | Configuration supplémentaire |
| Mise à jour | `apt update` | Pareil (dans WSL) |

## Ressources

- [Installer WSL](https://learn.microsoft.com/fr-fr/windows/wsl/install)
- [Windows Terminal](https://github.com/microsoft/terminal)
- [Ollama pour Windows](https://ollama.com/download)
