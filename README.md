# 🚀 Rôle Ansible WordPress

Rôle Ansible pour installer WordPress avec MariaDB sur Ubuntu et Rocky Linux.

## 📦 Installation du rôle

```bash
ansible-galaxy install Dexteria78.wordpress
```

## 🏗️ Structure de projet

```
projet/
├── inventaire                 # Vos serveurs
├── ansible.cfg               # Configuration Ansible
└── playbooks/                # Vos playbooks de test
    ├── test-ubuntu.yml
    ├── test-rocky.yml
    └── test-all.yml
```

## 🧪 Playbooks de test

### Test Ubuntu : `projet/playbooks/test-ubuntu.yml`
```yaml
---
- name: Test WordPress sur Ubuntu
  hosts: ubuntu
  become: yes
  vars:
    vault_db_password: "UbuntuPass123!"
    vault_db_root_password: "UbuntuRoot123!"
    db_name: "wordpress_ubuntu"
  roles:
    - Dexteria78.wordpress
  
  post_tasks:
    - name: Afficher l'URL d'accès
      debug:
        msg: "WordPress accessible sur http://{{ ansible_default_ipv4.address }}"
```

### Test Rocky Linux : `projet/playbooks/test-rocky.yml`
```yaml
---
- name: Test WordPress sur Rocky Linux
  hosts: rocky
  become: yes
  vars:
    vault_db_password: "RockyPass123!"
    vault_db_root_password: "RockyRoot123!"
    db_name: "wordpress_rocky"
  roles:
    - Dexteria78.wordpress
  
  post_tasks:
    - name: Afficher l'URL d'accès
      debug:
        msg: "WordPress accessible sur http://{{ ansible_default_ipv4.address }}"
```

### Test complet : `projet/playbooks/test-all.yml`
```yaml
---
- name: Test WordPress sur Ubuntu
  hosts: ubuntu
  become: yes
  vars:
    vault_db_password: "Pass123!"
    vault_db_root_password: "Root123!"
    db_name: "wp_ubuntu"
  roles:
    - Dexteria78.wordpress

- name: Test WordPress sur Rocky Linux
  hosts: rocky
  become: yes
  vars:
    vault_db_password: "Pass123!"
    vault_db_root_password: "Root123!"
    db_name: "wp_rocky"
  roles:
    - Dexteria78.wordpress

- name: Résultats finaux
  hosts: all
  tasks:
    - name: URLs d'accès WordPress
      debug:
        msg: "WordPress sur {{ inventory_hostname }} : http://{{ ansible_default_ipv4.address }}"
```

## 🚀 Lancement des tests

```bash
# Se positionner dans le répertoire projet
cd /projet

# Test Ubuntu uniquement
ansible-playbook playbooks/test-ubuntu.yml

# Test Rocky Linux uniquement
ansible-playbook playbooks/test-rocky.yml

# Test sur toutes les distributions
ansible-playbook playbooks/test-all.yml

```

## 🌐 Accès à WordPress

Après déploiement réussi :

1. **Récupérer l'IP du serveur** (affichée à la fin du playbook)
2. **Ouvrir dans le navigateur** : `http://IP_DU_SERVEUR`
3. **Suivre l'assistant WordPress** :
   - Sélectionner la langue
   - Renseigner les informations du site
   - Créer le compte administrateur

### Informations de base de données (si demandées)
- **Nom de la base** : `wordpress_ubuntu` ou `wordpress_rocky` (selon le playbook)
- **Utilisateur** : `wpuser`
- **Mot de passe** : Celui défini dans `vault_db_password`
- **Hôte** : `localhost`

## 🔧 Variables principales

| Variable | Description | Défaut |
|----------|-------------|--------|
| `vault_db_password` | Mot de passe base WordPress | **Obligatoire** |
| `vault_db_root_password` | Mot de passe root MariaDB | **Obligatoire** |
| `db_name` | Nom de la base de données | `wordpress` |
| `db_user` | Utilisateur de la base | `wpuser` |
| `wp_debug` | Mode debug WordPress | `false` |

## 🆘 Dépannage rapide

### Vérifier la connectivité
```bash
ansible all -m ping
```

### Vérifier les services
```bash
ansible all -m shell -a "systemctl status apache2 || systemctl status httpd"
ansible all -m shell -a "systemctl status mariadb"
```

## 📋 Checklist de validation

- [ ] Rôle installé : `ansible-galaxy list | grep wordpress`
- [ ] Connectivité OK : `ansible all -m ping`
- [ ] Playbook exécuté sans erreur
- [ ] WordPress accessible dans le navigateur
- [ ] Assistant d'installation WordPress affiché

---


**Rôle Galaxy** : `ansible-galaxy install Dexteria78.wordpress`  
