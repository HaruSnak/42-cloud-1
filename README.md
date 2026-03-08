<img src="readme/cloud-1.png" alt="cloud-1" width="900"/>

<div align="center">

# Cloud-1
### Automated deployment of Inception on a remote server at 42 School

[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![License][license-shield]][license-url]

</div>

---

## 🇬🇧 English

<details>
<summary><b>📖 Click to expand/collapse English version</b></summary>

### 📖 About

**Cloud-1** is a project for 42 School students focused on the automated deployment of a distributed web infrastructure. It builds upon the Inception project by taking the dockerized services and deploying them automatically on a remote cloud instance using Ansible. The application features a robust microservices architecture with dedicated containers for Nginx, WordPress, and MariaDB, all secured and optimized for cloud environments.

This project teaches:
- System Administration and Cloud Infrastructure
- Automated Provisioning and Configuration Management with Ansible
- Containerization with Docker and Docker Compose
- Network Security, TLS implementation, and Port Management
- Persistent Data Management and Resilience Configurations

### 🧠 Skills Learned

By completing the Cloud-1 project, students develop essential skills in system administration and DevOps:

- **Infrastructure as Code (IaC)**: Using Ansible to fully automate server configuration from a fresh OS installation.
- **Container Orchestration**: Managing multiple communicating containers where each process runs entirely isolated.
- **High Availability & Resilience**: Configuring automatic restarts and ensuring complete data persistence across server reboots.
- **Network & Security**: Implementing Nginx as a reverse proxy, setting up SSL/TLS certificates, and strictly limiting exposed ports to 80, 443, and 22.
- **Service Integration**: Integrating MariaDB, WordPress, and other tools into a cohesive and functional web application.

## Approach
This project was developed with a strong focus on modularity and security. The Ansible codebase is organized into clear components:

- **common**: Basic server setup, updates, and foundational tools.
- **docker_setup**: Installation and configuration of the Docker engine and Docker Compose.
- **inception_deploy**: Orchestration of the actual services, transferring files, and triggering docker-compose.

The application strictly follows the "1 process = 1 container" rule, ensuring an independent lifecycle for each service. All deployment steps are abstracted away so the entire infrastructure can be brought up with a single command.

### **Features**

**Automated Ansible Deployment:** *Scripts capable of bridging an empty Ubuntu server to a fully functional WordPress site.*<br>

**Microservices Architecture:** *Independent Docker containers for Nginx, WordPress, and MariaDB.*<br>

**Data Persistence:** *Docker volumes configured to retain all database data and website media across reboots.*<br>

**Security & TLS:** *Secured traffic via HTTPS and restricted external access to non-essential ports.*<br>

**Self-Healing Services:** *Containers configured with restart policies to recover automatically from failures or server restarts.*<br>

### **Features to be added:**

**Multi-Server Deployment:** *Extending the playbook to scale the app across multiple nodes.*<br>

**Advanced Monitoring:** *Integrating Prometheus and Grafana for real-time infrastructure metrics.*<br>

**Automated Backups:** *Cron jobs to backup volumes to secure remote storage.*<br>

### 📋 Table of Contents

- [Features](#features)
- [Installation](#installation)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [Development](#development)
- [Credits](#credits)

<a name="features"></a>

### ✨ Features

- **Automated Deployment** using Ansible
- **Containerized Infrastructure** using Docker and Docker Compose
- **Nginx Reverse Proxy** with SSL/TLS encryption
- **WordPress Integration** connected to a robust MariaDB backend
- **Persistent Storage** using Docker volumes for high resilience
- **Strict Network Policies** exposing only HTTP, HTTPS, and SSH ports

<a name="installation"></a>

### 🚀 Installation

```bash
# Clone the repository
git clone https://github.com/HaruSnak/42-cloud-1
cd 42-cloud-1

# Configure your inventory carefully with your server's IP
# Example in inventory/hosts.yml

# Run the Ansible deployment
ansible-playbook -i inventory/hosts.yml create_cloud-1.yml --ask-become-pass
```

<a name="usage"></a>

### 💻 Usage

Once the Ansible playbook completes, your remote cloud server will be fully configured.
Access the application:
- **Website**: https://<your_server_ip_or_domain>

<a name="project-structure"></a>

### 📂 Project Structure

```
42-cloud-1/
├── ansible.cfg                 # Ansible configuration file
├── create_cloud-1.yml          # Main Ansible playbook entry point
├── inventory/                  # Host definitions and inventory
├── group_vars/                 # Variables applied to groups of hosts
├── host_vars/                  # Variables applied to specific hosts
├── vault/                      # Encrypted secrets and passwords
├── roles/                      # Ansible roles
│   ├── common/                 # Server baseline configuration
│   ├── docker_setup/           # Docker suite installation
│   └── inception_deploy/       # Application and container deployment
└── README.md                   # This file
```

<a name="development"></a>

### 🔧 Development

#### Prerequisites
- Ansible installed on your control node
- A remote server running an Ubuntu LTS OS with an SSH daemon and Python installed
- Make sure you possess the relevant SSH keys and have configured the vault correctly

#### Environment Setup
```bash
# Ensure your SSH key is authorized on the remote server
ssh-copy-id user@your_server_ip

# Test the connection to the host
ansible all -m ping -i inventory/hosts.yml
```

### 👨‍🎓 Note
<p align="left">
<img width="198" height="171" alt="image" src="https://github.com/user-attachments/assets/2c722f1b-b820-4dd7-b813-ccaaa2600c3c" />

</p>

<a name="credits"></a>

### 📖 Credits

- **Documentation Ansible** : [Ansible.com](https://docs.ansible.com/)
- **Documentation Docker** : [Docker.com](https://docs.docker.com/)
- **Documentation Nginx** : [Nginx.org](https://nginx.org/en/docs/)

### 📄 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

</details>

---

## 🇫🇷 Français

<details>
<summary><b>📖 Cliquez pour développer/réduire la version française</b></summary>

### 📖 À propos

**Cloud-1** est un projet pour les étudiants de l'école 42 axé sur le déploiement automatisé d'une infrastructure web distribuée. Il s'appuie sur le projet Inception en reprenant les services dockerisés et en les déployant automatiquement sur une instance cloud distante à l'aide d'Ansible. L'application présente une architecture microservices robuste avec des conteneurs dédiés pour Nginx, WordPress et MariaDB, le tout sécurisé et optimisé pour les environnements cloud.

Ce projet enseigne :
- L'administration système et l'infrastructure Cloud
- Le provisionnement automatisé et la gestion de configuration avec Ansible
- La conteneurisation avec Docker et Docker Compose
- La sécurité réseau, l'implémentation TLS et la gestion des ports
- La gestion des données persistantes et les configurations de résilience

### 🧠 Compétences acquises

En complétant le projet Cloud-1, les étudiants développent des compétences essentielles en administration système et DevOps :

- **Infrastructure as Code (IaC)** : Utilisation d'Ansible pour automatiser entièrement la configuration du serveur à partir d'une installation d'OS vierge.
- **Orchestration de Conteneurs** : Gestion de multiples conteneurs communicants où chaque processus s'exécute de manière entièrement isolée.
- **Haute Disponibilité & Résilience** : Configuration des redémarrages automatiques et assurance de la persistance complète des données après redémarrage du serveur.
- **Réseau & Sécurité** : Implémentation de Nginx comme reverse proxy, configuration des certificats SSL/TLS et limitation stricte des ports exposés à 80, 443 et 22.
- **Intégration de Services** : Intégration de MariaDB, WordPress et autres outils dans une application web fonctionnelle et cohérente.

## Approche
Ce projet a été développé avec une forte attention sur la modularité et la sécurité. La base de code Ansible est organisée en composants clairs :

- **common** : Configuration de base du serveur, mises à jour et outils fondamentaux.
- **docker_setup** : Installation et configuration du moteur Docker et de Docker Compose.
- **inception_deploy** : Orchestration des services réels, transfert de fichiers et déclenchement de docker-compose.

L'application suit strictement la règle "1 processus = 1 conteneur", garantissant un cycle de vie indépendant pour chaque service. Toutes les étapes de déploiement sont abstraites afin que l'infrastructure complète puisse être lancée avec une seule commande.

### **Fonctionnalités**

**Déploiement Ansible Automatisé :** *Scripts capables de transformer un serveur Ubuntu vide en un site WordPress entièrement fonctionnel.*<br>

**Architecture Microservices :** *Conteneurs Docker indépendants pour Nginx, WordPress et MariaDB.*<br>

**Persistance des Données :** *Volumes Docker configurés pour conserver toutes les données de la base de données et les médias du site web après redémarrage.*<br>

**Sécurité & TLS :** *Trafic sécurisé via HTTPS et accès externe limité aux ports non essentiels.*<br>

**Services Auto-réparateurs :** *Conteneurs configurés avec des politiques de redémarrage pour se rétablir automatiquement après des pannes ou redémarrages.*<br>

### **Fonctionnalités à ajouter :**

**Déploiement Multi-Serveurs :** *Extension du playbook pour déployer l'application sur plusieurs nœuds.*<br>

**Surveillance Avancée :** *Intégration de Prometheus et Grafana pour obtenir des métriques d'infrastructure en temps réel.*<br>

**Sauvegardes Automatisées :** *Tâches Cron pour sauvegarder les volumes vers un stockage distant sécurisé.*<br>

### 📋 Table des matières

- [Caractéristiques](#caractéristiques)
- [Installation](#installation-1)
- [Utilisation](#utilisation)
- [Structure du projet](#structure-du-projet)
- [Développement](#développement)
- [Crédits](#crédits-1)

<a name="caractéristiques"></a>

### ✨ Caractéristiques

- **Déploiement Automatisé** utilisant Ansible
- **Infrastructure Conteneurisée** utilisant Docker et Docker Compose
- **Reverse Proxy Nginx** avec chiffrement SSL/TLS
- **Intégration WordPress** connecté à un backend MariaDB robuste
- **Stockage Persistant** utilisant des volumes Docker pour une haute résilience
- **Politiques Réseau Strictes** exposant uniquement les ports HTTP, HTTPS et SSH

<a name="installation-1"></a>

### 🚀 Installation

```bash
# Cloner le dépôt
git clone https://github.com/HaruSnak/42-cloud-1
cd 42-cloud-1

# Configurer votre inventaire avec l'IP de votre serveur
# Exemple dans inventory/hosts.yml

# Lancer le déploiement Ansible
ansible-playbook -i inventory/hosts.yml create_cloud-1.yml --ask-become-pass
```

<a name="utilisation"></a>

### 💻 Utilisation

Une fois le playbook Ansible terminé, votre serveur cloud distant sera entièrement configuré.
Accéder à l'application :
- **Site Web** : https://<votre_ip_serveur_ou_domaine>

<a name="structure-du-projet"></a>

### 📂 Structure du projet

```
42-cloud-1/
├── ansible.cfg                 # Fichier de configuration Ansible
├── create_cloud-1.yml          # Point d'entrée principal du playbook Ansible
├── inventory/                  # Définition des hôtes et inventaire
├── group_vars/                 # Variables appliquées aux groupes d'hôtes
├── host_vars/                  # Variables appliquées aux hôtes spécifiques
├── vault/                      # Secrets et mots de passe chiffrés
├── roles/                      # Rôles Ansible
│   ├── common/                 # Configuration de base du serveur
│   ├── docker_setup/           # Installation de la suite Docker
│   └── inception_deploy/       # Déploiement de l'application et des conteneurs
└── README.md                   # Ce fichier
```

<a name="développement"></a>

### 🔧 Développement

#### Prérequis
- Ansible installé sur votre machine de contrôle
- Un serveur distant sous Ubuntu LTS avec un démon SSH et Python installés
- Assurez-vous de posséder les clés SSH adéquates et d'avoir configuré le coffre-fort (vault) correctement.

#### Configuration de l'environnement
```bash
# S'assurer que votre clé SSH est autorisée sur le serveur distant
ssh-copy-id utilisateur@ip_de_votre_serveur

# Tester la connexion à l'hôte
ansible all -m ping -i inventory/hosts.yml
```

### 👨‍🎓 Note
<p align="left">
<img width="198" height="171" alt="image" src="https://github.com/user-attachments/assets/c6611943-f93e-4905-9548-4a6dbce0a951" />

</p>

<a name="crédits-1"></a>

### 📖 Crédits

- **Documentation Ansible** : [Ansible.com](https://docs.ansible.com/)
- **Documentation Docker** : [Docker.com](https://docs.docker.com/)
- **Documentation Nginx** : [Nginx.org](https://nginx.org/en/docs/)

### 📄 Licence

Ce projet est sous licence **MIT** - voir le fichier [LICENSE](LICENSE) pour plus de détails.

</details>

---

[contributors-shield]: https://img.shields.io/github/contributors/HaruSnak/42-cloud-1.svg?style=for-the-badge
[contributors-url]: https://github.com/HaruSnak/42-cloud-1/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/HaruSnak/42-cloud-1.svg?style=for-the-badge
[forks-url]: https://github.com/HaruSnak/42-cloud-1/network/members
[stars-shield]: https://img.shields.io/github/stars/HaruSnak/42-cloud-1.svg?style=for-the-badge
[stars-url]: https://github.com/HaruSnak/42-cloud-1/stargazers
[issues-shield]: https://img.shields.io/github/issues/HaruSnak/42-cloud-1.svg?style=for-the-badge
[issues-url]: https://github.com/HaruSnak/42-cloud-1/issues
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://www.linkedin.com/in/shany-moreno-5a863b2aa
[license-shield]: https://img.shields.io/github/license/HaruSnak/42-cloud-1.svg?style=for-the-badge
[license-url]: https://github.com/HaruSnak/42-cloud-1/blob/master/LICENSE
