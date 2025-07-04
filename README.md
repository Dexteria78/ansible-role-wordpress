# ansible-role-wordpress

[![CI](https://github.com/Dexteria78/ansible-role-wordpress/workflows/CI/badge.svg)](https://github.com/Dexteria78/ansible-role-wordpress/actions)
[![Ansible Galaxy](https://img.shields.io/badge/galaxy-dexteria78.wordpress-blue.svg)](https://galaxy.ansible.com/dexteria78/wordpress)

Ce rôle Ansible automatise l'installation et la configuration de WordPress avec MariaDB sur Ubuntu et Rocky Linux.

## Description

Ce rôle installe et configure :
- WordPress (dernière version)
- MariaDB (serveur de base de données)
- Apache2/httpd (serveur web)
- PHP et extensions nécessaires
- Configuration sécurisée

## Compatibilité

| Distribution | Versions testées |
|--------------|------------------|
| Ubuntu       | 20.04, 22.04     |
| Rocky Linux  | 8.x, 9.x         |

**Versions Ansible supportées :** 2.9+

## Installation

### Depuis Ansible Galaxy
```bash
ansible-galaxy install dexteria78.wordpress
```

### Depuis GitHub
```bash
ansible-galaxy install git+https://github.com/Dexteria78/ansible-role-wordpress.git
```

## Variables

### Variables principales

| Variable | Défaut | Description |
|----------|--------|-------------|
| `wordpress_version` | `latest` | Version de WordPress à installer |
| `wordpress_path` | `/var/www/html` | Répertoire d'installation de WordPress |
| `db_name` | `wordpress` | Nom de la base de données |
| `db_user` | `wpuser` | Utilisateur de la base de données |
| `db_host` | `localhost` | Hôte de la base de données |

### Variables sensibles (à définir avec Ansible Vault)

| Variable | Description |
|----------|-------------|
| `vault_db_password` | Mot de passe de l'utilisateur de la base |
| `vault_db_root_password` | Mot de passe root de MariaDB |

### Variables de débogage

| Variable | Défaut | Description |
|----------|--------|-------------|
| `wp_debug` | `false` | Active le mode debug WordPress |
| `wp_debug_log` | `false` | Active les logs de debug |
| `wp_debug_display` | `false` | Affiche les erreurs à l'écran |

## Utilisation

### Playbook basique
```yaml
---
- name: Install WordPress
  hosts: webservers
  become: yes
  vars:
    vault_db_password: "MonMotDePasseSecurise!"
    vault_db_root_password: "MonMotDePasseRootSecurise!"
  roles:
    - dexteria78.wordpress
```

### Playbook avec variables personnalisées
```yaml
---
- name: Install WordPress with custom settings
  hosts: webservers
  become: yes
  vars:
    # Base de données
    db_name: "mon_site_wp"
    db_user: "mon_utilisateur"
    vault_db_password: "{{ vault_wp_password }}"
    vault_db_root_password: "{{ vault_mysql_root }}"
    
    # WordPress
    wp_debug: true
    apache_server_admin: "admin@mondomaine.com"
  roles:
    - dexteria78.wordpress
```

### Utilisation avec Ansible Vault
```bash
# Créer un fichier de variables chiffrées
ansible-vault create vars/secrets.yml

# Contenu du fichier secrets.yml:
vault_db_password: "MotDePasseTresSecurise123!"
vault_db_root_password: "MotDePasseRootTresSecurise123!"

# Utiliser dans le playbook
ansible-playbook site.yml --ask-vault-pass -e @vars/secrets.yml
```

## Dépendances

### Collections Ansible requises
```bash
ansible-galaxy collection install community.mysql
ansible-galaxy collection install ansible.posix
```

### Packages système
Le rôle installe automatiquement tous les packages nécessaires.

## Handlers

- `restart web server` : Redémarre Apache2/httpd
- `reload web server` : Recharge la configuration Apache2/httpd  
- `restart mariadb` : Redémarre le service MariaDB

## Tests

### Test local
```bash
# Cloner le repo
git clone https://github.com/Dexteria78/ansible-role-wordpress.git
cd ansible-role-wordpress

# Installer les dépendances
ansible-galaxy collection install community.mysql ansible.posix

# Exécuter les tests
ansible-playbook tests/test.yml -i tests/inventory --connection=local
```

### Tests CI/CD
Le rôle inclut des tests automatisés via GitHub Actions qui vérifient :
- Syntaxe Ansible
- Ansible-lint
- Tests fonctionnels

## Sécurité

⚠️ **Important** : 
- Toujours utiliser Ansible Vault pour les mots de passe
- Changer les mots de passe par défaut
- Configurer HTTPS en production
- Maintenir WordPress à jour

### Exemple avec Vault
```bash
# Créer un mot de passe vault
echo 'mon_mot_de_passe_vault' > .vault_pass

# Chiffrer les variables sensibles
ansible-vault encrypt_string 'MonMotDePasseDB' --name 'vault_db_password'

# Utiliser dans le playbook
ansible-playbook site.yml --vault-password-file .vault_pass
```

## Contribution

1. Fork le projet
2. Créer une branche pour votre fonctionnalité
3. Commiter vos changements
4. Pousser vers la branche
5. Ouvrir une Pull Request

## Licence

MIT
