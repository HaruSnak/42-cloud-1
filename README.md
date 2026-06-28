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

**Cloud-1** is a project for 42 School students focused on the automated deployment of a distributed web infrastructure. It builds upon the Inception project by taking the dockerized services and deploying them automatically on a remote cloud instance using Ansible. The application features a microservices architecture with dedicated containers for Nginx, WordPress, MariaDB and phpMyAdmin, all secured and optimized for cloud environments.

This project teaches:
- System Administration and Cloud Infrastructure
- Automated Provisioning and Configuration Management with Ansible
- Containerization with Docker and Docker Compose
- Network Security, TLS implementation, and Port Management
- Persistent Data Management and Resilience Configurations

### 🏗️ Architecture

```
                            INTERNET
                                │
               ┌────────────────┼──────────────────┐
               │ port 22 (SSH)  │  port 80/443 (HTTP/HTTPS)
               │                ▼
               │          [ UFW Firewall ]
               │       (blocks all other ports)
               ▼
    ┌──────────────────────────────────────────────────┐
    │                 Ubuntu 22.04 Host                 │
    │                                                  │
    │   ┌──────────────────────────────────────────┐   │
    │   │         Docker Network: cloud_network     │   │
    │   │                                          │   │
    │   │   ┌───────────────────────────────────┐  │   │
    │   │   │             nginx                 │  │   │
    │   │   │  :80  → 301 redirect to HTTPS     │  │   │
    │   │   │  :443 → TLS 1.2/1.3 termination   │  │   │
    │   │   │  /        → WordPress (FastCGI)   │  │   │
    │   │   │  /phpmyadmin/ → phpMyAdmin proxy  │  │   │
    │   │   └──────┬─────────────────┬──────────┘  │   │
    │   │          │ FastCGI :9000   │ HTTP proxy   │   │
    │   │   ┌──────▼──────┐  ┌──────▼──────────┐  │   │
    │   │   │  wordpress  │  │   phpmyadmin    │  │   │
    │   │   │  (php-fpm)  │  │  (web UI only)  │  │   │
    │   │   └──────┬──────┘  └──────┬──────────┘  │   │
    │   │          └────────┬────────┘             │   │
    │   │                   │ :3306 (internal only) │   │
    │   │          ┌────────▼────────┐             │   │
    │   │          │    mariadb      │             │   │
    │   │          │ (not exposed)   │             │   │
    │   │          └─────────────────┘             │   │
    │   └──────────────────────────────────────────┘   │
    │                                                  │
    │   Persistent bind-mounts:                        │
    │   ~/data/wordpress  ~/data/mariadb  ~/certs      │
    └──────────────────────────────────────────────────┘
```

**Data flow:**
- All external traffic enters only through ports 80, 443, and 22.
- HTTP is immediately redirected to HTTPS by nginx.
- WordPress is served via PHP-FPM (FastCGI), never exposed directly.
- phpMyAdmin is accessible only via the `/phpmyadmin/` path, proxied through nginx over HTTPS.
- MariaDB is reachable only from within the Docker network — never from the internet.
- All service data survives reboots via host-bound Docker volumes.

### 🧱 Ansible Roles

| Role | Responsibility |
|---|---|
| `common` | System updates, UFW firewall (ports 22/80/443), data directories, root SSH access |
| `docker_setup` | Install Docker + Docker Compose, enable Docker daemon on boot |
| `inception_deploy` | Deploy docker-compose stack, generate TLS certificate, install and configure WordPress via WP-CLI |

### ✨ Features

- **Fully Automated Deployment** — from a bare Ubuntu 22.04 instance to a running WordPress site with a single command
- **1 Process = 1 Container** — nginx, wordpress, mariadb, phpmyadmin each in their own container
- **TLS/HTTPS** — self-signed certificate generated at deploy time via OpenSSL
- **Data Persistence** — Docker volumes bound to host paths survive server reboots
- **Auto-restart on Reboot** — all containers use `restart: always`; Docker daemon is systemd-enabled
- **Strict Firewall** — UFW blocks all ports except 22, 80, 443
- **Multi-Server Support** — the `clouds` inventory group supports any number of parallel targets
- **Vault-encrypted Secrets** — credentials stored with Ansible Vault, never in plaintext

### 📋 Table of Contents

- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Multi-Server Deployment](#multi-server-deployment)
- [Domain Configuration](#domain-configuration)
- [Project Structure](#project-structure)
- [Credits](#credits)

<a name="prerequisites"></a>

### 🔧 Prerequisites

On your **control node** (your local machine):
- Ansible ≥ 2.12
- Required Ansible collections (install once):

```bash
ansible-galaxy collection install -r requirements.yml
```

On the **remote server**:
- Ubuntu 22.04 LTS (or compatible)
- SSH daemon running
- Python 3 installed
- Your SSH public key in `~/.ssh/authorized_keys`

<a name="installation"></a>

### 🚀 Installation

```bash
# 1. Clone the repository
git clone https://github.com/HaruSnak/42-cloud-1
cd 42-cloud-1

# 2. Install required Ansible collections
ansible-galaxy collection install -r requirements.yml

# 3. Edit the inventory with your server's IP and user
#    vim inventory/hosts.yml

# 4. Edit host_vars/cloud1.yml to set your domain name
#    vim host_vars/cloud1.yml

# 5. Run the playbook (Vault password required to decrypt credentials)
ansible-playbook create_cloud-1.yml --ask-vault-pass
```

Once complete, access your site at:
- **WordPress**: `https://<your_domain_or_ip>/`
- **phpMyAdmin**: `https://<your_domain_or_ip>/phpmyadmin/`

<a name="multi-server-deployment"></a>

### 🖥️ Multi-Server Deployment

The playbook targets the `clouds` group, which supports any number of hosts. To deploy on multiple servers in parallel, add entries to `inventory/hosts.yml`:

```yaml
all:
  children:
    clouds:
      hosts:
        cloud1:
          ansible_host: 1.2.3.4
          ansible_user: ubuntu
          ansible_become_pass: "{{ become_password }}"
        cloud2:
          ansible_host: 5.6.7.8
          ansible_user: ubuntu
          ansible_become_pass: "{{ become_password }}"
      vars:
        ansible_python_interpreter: /usr/bin/python3
```

Ansible will provision all hosts in the group simultaneously.

<a name="domain-configuration"></a>

### 🌐 Domain Configuration

The domain name is set per-host in `host_vars/<hostname>.yml`:

```yaml
# host_vars/cloud1.yml
DOMAIN_NAME: yourdomain.example.com
```

The TLS certificate is generated with this common name at deploy time. You can use:
- A free subdomain from [DuckDNS](https://www.duckdns.org/) pointing to your server IP
- Your server's public IP directly (browser will warn about self-signed cert)

<a name="project-structure"></a>

### 📂 Project Structure

```
42-cloud-1/
├── ansible.cfg                         # Ansible configuration
├── create_cloud-1.yml                  # Main playbook entry point
├── requirements.yml                    # Ansible collection dependencies
├── inventory/
│   └── hosts.yml                       # Target host definitions
├── group_vars/
│   └── all.yml                         # Vault-encrypted become password
├── host_vars/
│   └── cloud1.yml                      # Per-host vars (DOMAIN_NAME)
└── roles/
    ├── common/                         # System baseline + firewall
    │   ├── tasks/main.yml
    │   └── handlers/main.yml
    ├── docker_setup/                   # Docker + Docker Compose install
    │   └── tasks/main.yml
    └── inception_deploy/               # Application stack deployment
        ├── tasks/main.yml
        ├── templates/
        │   ├── docker-compose.yml.j2   # Stack definition
        │   └── nginx.conf.j2           # Reverse proxy config
        └── files/
            └── .env                    # Vault-encrypted credentials
```

<a name="credits"></a>

### 📖 Credits

- **Ansible Documentation**: [docs.ansible.com](https://docs.ansible.com/)
- **Docker Documentation**: [docs.docker.com](https://docs.docker.com/)
- **Nginx Documentation**: [nginx.org/en/docs](https://nginx.org/en/docs/)
- **WP-CLI**: [wp-cli.org](https://wp-cli.org/)

### 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

</details>

---

## 🇫🇷 Français

<details>
<summary><b>📖 Cliquez pour développer/réduire la version française</b></summary>

### 📖 À propos

**Cloud-1** est un projet pour les étudiants de l'école 42 axé sur le déploiement automatisé d'une infrastructure web distribuée. Il s'appuie sur le projet Inception en reprenant les services dockerisés et en les déployant automatiquement sur une instance cloud distante à l'aide d'Ansible. L'application présente une architecture microservices avec des conteneurs dédiés pour Nginx, WordPress, MariaDB et phpMyAdmin, le tout sécurisé et optimisé pour les environnements cloud.

Ce projet enseigne :
- L'administration système et l'infrastructure Cloud
- Le provisionnement automatisé et la gestion de configuration avec Ansible
- La conteneurisation avec Docker et Docker Compose
- La sécurité réseau, l'implémentation TLS et la gestion des ports
- La gestion des données persistantes et les configurations de résilience

### 🏗️ Architecture

```
                            INTERNET
                                │
               ┌────────────────┼──────────────────┐
               │ port 22 (SSH)  │  port 80/443 (HTTP/HTTPS)
               │                ▼
               │          [ Pare-feu UFW ]
               │       (tous les autres ports bloqués)
               ▼
    ┌──────────────────────────────────────────────────┐
    │                 Hôte Ubuntu 22.04                 │
    │                                                  │
    │   ┌──────────────────────────────────────────┐   │
    │   │       Réseau Docker : cloud_network       │   │
    │   │                                          │   │
    │   │   ┌───────────────────────────────────┐  │   │
    │   │   │             nginx                 │  │   │
    │   │   │  :80  → redirection 301 vers HTTPS│  │   │
    │   │   │  :443 → terminaison TLS 1.2/1.3   │  │   │
    │   │   │  /        → WordPress (FastCGI)   │  │   │
    │   │   │  /phpmyadmin/ → proxy phpMyAdmin  │  │   │
    │   │   └──────┬─────────────────┬──────────┘  │   │
    │   │          │ FastCGI :9000   │ proxy HTTP   │   │
    │   │   ┌──────▼──────┐  ┌──────▼──────────┐  │   │
    │   │   │  wordpress  │  │   phpmyadmin    │  │   │
    │   │   │  (php-fpm)  │  │  (interface web)│  │   │
    │   │   └──────┬──────┘  └──────┬──────────┘  │   │
    │   │          └────────┬────────┘             │   │
    │   │                   │ :3306 (interne seul.) │   │
    │   │          ┌────────▼────────┐             │   │
    │   │          │    mariadb      │             │   │
    │   │          │ (non exposée)   │             │   │
    │   │          └─────────────────┘             │   │
    │   └──────────────────────────────────────────┘   │
    │                                                  │
    │   Volumes persistants (bind-mounts) :            │
    │   ~/data/wordpress  ~/data/mariadb  ~/certs      │
    └──────────────────────────────────────────────────┘
```

**Flux de données :**
- Tout le trafic externe entre uniquement par les ports 80, 443 et 22.
- Le HTTP est immédiatement redirigé vers HTTPS par nginx.
- WordPress est servi via PHP-FPM (FastCGI), jamais exposé directement.
- phpMyAdmin est accessible uniquement via le chemin `/phpmyadmin/`, proxifié par nginx.
- MariaDB est joignable uniquement depuis l'intérieur du réseau Docker — jamais depuis internet.
- Toutes les données des services survivent aux redémarrages grâce aux volumes Docker liés à l'hôte.

### 🧱 Rôles Ansible

| Rôle | Responsabilité |
|---|---|
| `common` | Mises à jour système, pare-feu UFW (ports 22/80/443), répertoires de données, accès SSH root |
| `docker_setup` | Installation Docker + Docker Compose, activation du daemon Docker au démarrage |
| `inception_deploy` | Déploiement du stack docker-compose, génération du certificat TLS, installation et configuration de WordPress via WP-CLI |

### ✨ Fonctionnalités

- **Déploiement entièrement automatisé** — d'une instance Ubuntu 22.04 vierge à un site WordPress fonctionnel en une seule commande
- **1 processus = 1 conteneur** — nginx, wordpress, mariadb, phpmyadmin chacun dans son propre conteneur
- **TLS/HTTPS** — certificat auto-signé généré au moment du déploiement via OpenSSL
- **Persistance des données** — volumes Docker liés aux chemins hôtes, survivent aux redémarrages serveur
- **Redémarrage automatique** — tous les conteneurs utilisent `restart: always` ; le daemon Docker est activé via systemd
- **Pare-feu strict** — UFW bloque tous les ports sauf 22, 80, 443
- **Déploiement multi-serveurs** — le groupe d'inventaire `clouds` supporte un nombre quelconque de cibles en parallèle
- **Secrets chiffrés** — identifiants stockés avec Ansible Vault, jamais en clair

### 📋 Table des matières

- [Architecture](#architecture-1)
- [Prérequis](#prérequis)
- [Installation](#installation-1)
- [Déploiement multi-serveurs](#déploiement-multi-serveurs)
- [Configuration du domaine](#configuration-du-domaine)
- [Structure du projet](#structure-du-projet)
- [Crédits](#crédits)

<a name="prérequis"></a>

### 🔧 Prérequis

Sur votre **machine de contrôle** (en local) :
- Ansible ≥ 2.12
- Collections Ansible requises (à installer une fois) :

```bash
ansible-galaxy collection install -r requirements.yml
```

Sur le **serveur distant** :
- Ubuntu 22.04 LTS (ou compatible)
- Daemon SSH actif
- Python 3 installé
- Votre clé SSH publique dans `~/.ssh/authorized_keys`

<a name="installation-1"></a>

### 🚀 Installation

```bash
# 1. Cloner le dépôt
git clone https://github.com/HaruSnak/42-cloud-1
cd 42-cloud-1

# 2. Installer les collections Ansible requises
ansible-galaxy collection install -r requirements.yml

# 3. Renseigner l'IP et l'utilisateur de votre serveur
#    vim inventory/hosts.yml

# 4. Définir votre nom de domaine dans host_vars/cloud1.yml
#    vim host_vars/cloud1.yml

# 5. Lancer le playbook (mot de passe Vault requis pour déchiffrer les credentials)
ansible-playbook create_cloud-1.yml --ask-vault-pass
```

Une fois le déploiement terminé :
- **WordPress** : `https://<votre_domaine_ou_ip>/`
- **phpMyAdmin** : `https://<votre_domaine_ou_ip>/phpmyadmin/`

<a name="déploiement-multi-serveurs"></a>

### 🖥️ Déploiement multi-serveurs

Le playbook cible le groupe `clouds`, qui supporte un nombre quelconque d'hôtes. Pour déployer sur plusieurs serveurs en parallèle, ajoutez des entrées dans `inventory/hosts.yml` :

```yaml
all:
  children:
    clouds:
      hosts:
        cloud1:
          ansible_host: 1.2.3.4
          ansible_user: ubuntu
          ansible_become_pass: "{{ become_password }}"
        cloud2:
          ansible_host: 5.6.7.8
          ansible_user: ubuntu
          ansible_become_pass: "{{ become_password }}"
      vars:
        ansible_python_interpreter: /usr/bin/python3
```

Ansible provisionnera tous les hôtes du groupe simultanément.

<a name="configuration-du-domaine"></a>

### 🌐 Configuration du domaine

Le nom de domaine est défini par hôte dans `host_vars/<nom_hote>.yml` :

```yaml
# host_vars/cloud1.yml
DOMAIN_NAME: votredomaine.example.com
```

Le certificat TLS est généré avec ce nom commun au moment du déploiement. Options possibles :
- Un sous-domaine gratuit via [DuckDNS](https://www.duckdns.org/) pointant vers votre IP
- Directement l'IP publique du serveur (le navigateur avertira du certificat auto-signé)

<a name="structure-du-projet"></a>

### 📂 Structure du projet

```
42-cloud-1/
├── ansible.cfg                         # Configuration Ansible
├── create_cloud-1.yml                  # Point d'entrée du playbook principal
├── requirements.yml                    # Dépendances de collections Ansible
├── inventory/
│   └── hosts.yml                       # Définition des hôtes cibles
├── group_vars/
│   └── all.yml                         # Mot de passe become chiffré (Vault)
├── host_vars/
│   └── cloud1.yml                      # Variables par hôte (DOMAIN_NAME)
└── roles/
    ├── common/                         # Baseline système + pare-feu
    │   ├── tasks/main.yml
    │   └── handlers/main.yml
    ├── docker_setup/                   # Installation Docker + Docker Compose
    │   └── tasks/main.yml
    └── inception_deploy/               # Déploiement du stack applicatif
        ├── tasks/main.yml
        ├── templates/
        │   ├── docker-compose.yml.j2   # Définition du stack
        │   └── nginx.conf.j2           # Configuration du reverse proxy
        └── files/
            └── .env                    # Credentials chiffrés (Vault)
```

<a name="crédits"></a>

### 📖 Crédits

- **Documentation Ansible** : [docs.ansible.com](https://docs.ansible.com/)
- **Documentation Docker** : [docs.docker.com](https://docs.docker.com/)
- **Documentation Nginx** : [nginx.org/en/docs](https://nginx.org/en/docs/)
- **WP-CLI** : [wp-cli.org](https://wp-cli.org/)

### 📄 Licence

Ce projet est sous licence **MIT** — voir le fichier [LICENSE](LICENSE) pour plus de détails.

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
