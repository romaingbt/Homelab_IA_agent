# Homelab_IA_agent
Plateforme d'infrastructure virtuelle et d'automatisation sur-mesure (Proxmox, Debian, Docker) dédiée à l'hébergement de modèles LLM locaux (Ollama) et de workflows d'agents IA autonomes (n8n).

# 🚀 Homelab_IA_agent

> Plateforme d'infrastructure virtuelle et d'automatisation sur-mesure (Proxmox, Debian, Docker) dédiée à l'hébergement de modèles LLM locaux (Ollama) et de workflows d'agents IA autonomes (n8n). 🚀🤖

---

## 🛠️ Stack Technique

* **Hyperviseur (Bare-metal) :** Proxmox VE 9.x
* **Système d'Exploitation VM :** Debian 13 Server (CLI)
* **Conteneurisation :** Docker & Docker Compose
* **Moteur d'IA Locale :** Ollama (`qwen2.5`, `llama3`)
* **Orchestration & Workflows :** n8n
* **Intégration & APIs :** Notion API, Discord Webhooks, Flux RSS

---

## 📐 Architecture du Lab

```text
[ Proxmox VE (Bare-Metal) ]
       │
       └──> [ VM Debian Server (CLI) ]
                 │
                 ├──> [ Docker Engine ]
                 │         │
                 │         ├──> [ Ollama ] ──> (LLM locaux : Qwen 2.5 / Llama 3)
                 │         ├──> [ n8n ]    ──> (Moteur de Workflows & Agents)
                 │         └──> [ Services Tiers / Bdd ]
                 │
                 └──> [ Intégrations Externes (Notion, Discord, APIs) ]
