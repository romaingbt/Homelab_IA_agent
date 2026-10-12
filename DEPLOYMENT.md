# 📋 Guide de Déploiement Pas à Pas

Ce document détaille l'ensemble des étapes techniques nécessaires pour reproduire l'infrastructure de ce Home Lab, de la configuration de l'hyperviseur jusqu'au lancement des conteneurs de la stack IA.

---

### Étape 1 : Création & Préparation de la VM Debian sur Proxmox VE
1. Télécharger l'ISO **Debian 12 Netinst** sur Proxmox.
2. Créer une nouvelle VM (ex: ID `100`) :
   - **CPU :** 4 Cores
   - **RAM :** 8 Go à 16 Go
   - **Disque :** 50 Go NVMe (VirtIO SCSI)
   - **Réseau :** Bridge `vmbr0`
3. Installer Debian sans environnement graphique (cocher uniquement *SSH server* et *standard system utilities*).

---

### Étape 2 : Durcissement & Configuration de Debian (CLI)

1. Connecte-toi en SSH à ta VM Debian :
   ```bash
   ssh utilisateur@IP_DEBIAN
Mets à jour le système et installe les paquets de base :

Bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl wget git htop ufw
Configure le pare-feu local (UFW) :

Bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp      # SSH
sudo ufw enable
Étape 3 : Installation de Docker & Docker Compose
Exécute le script d'installation officiel de Docker :

Bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
Ajoute ton utilisateur au groupe docker :

Bash
sudo usermod -aG docker $USER
newgrp docker
Vérifie que Docker fonctionne correctement :

Bash
docker --version
docker compose version
Étape 4 : Déploiement de la Stack IA & Automation (Ollama + n8n)
Crée un dossier dédié pour ton projet sur la VM :

Bash
mkdir -p ~/homelab-ai
cd ~/homelab-ai
Crée un fichier docker-compose.yml :

Bash
nano docker-compose.yml
Colle la configuration suivante dans le fichier :

YAML
version: '3.8'

networks:
  ai_network:
    driver: bridge

volumes:
  n8n_data:
  ollama_data:

services:
  # Moteur LLM Local
  ollama:
    image: ollama/ollama:latest
    container_name: ollama
    restart: always
    ports:
      - "11434:11434"
    volumes:
      - ollama_data:/root/.ollama
    networks:
      - ai_network

  # Moteur d'Automatisation & Agents
  n8n:
    image: docker.n8n.io/n8nio/n8n:latest
    container_name: n8n
    restart: always
    ports:
      - "5678:5678"
    environment:
      - N8N_HOST=localhost
      - N8N_PORT=5678
      - N8N_PROTOCOL=http
      - NODE_ENV=production
      - WEBHOOK_URL=http://localhost:5678/
    volumes:
      - n8n_data:/home/node/.n8n
    networks:
      - ai_network
    depends_on:
      - ollama
Lance la stack Docker en arrière-plan :

Bash
docker compose up -d
Étape 5 : Téléchargement du Modèle IA (Ollama)
Télécharge le modèle Qwen 2.5 (3B) adapté à la structuration de données :

Bash
docker exec -it ollama ollama run qwen2.5:3b
(Optionnel) Télécharge d'autres modèles selon tes besoins :

Bash
docker exec -it ollama ollama pull llama3
Étape 6 : Accès & Interconnexion n8n
Ouvre ton navigateur et va sur http://IP_DE_TA_VM_DEBIAN:5678.

Crée ton compte administrateur n8n.

Dans n8n, pour connecter ton nœud Ollama, utilise l'URL interne du conteneur :

Plaintext
http://ollama:11434
