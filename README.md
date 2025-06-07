# creation-espace-AULA
Décrit le déploiement de l'espace openWEBUI pour AULA


# Déploiement sécurisé d’Open WebUI pour l’agence AULA

## Objectif

Permettre aux utilisateurs de l’agence d’urbanisme AULA d’accéder à leur propre instance d’Open WebUI via un sous-domaine dédié, sécurisé, sans utiliser de numéro de port dans l’URL.

---

## Configuration finale

- Domaine d’accès : **https://aula-experimentation.public-ia.fr**
- Hébergement : **Instance Scaleway**
- Port interne : **3000** (conteneur Docker Open WebUI)
- Reverse proxy : **Nginx**
- Certificat SSL : **Let’s Encrypt avec Certbot**
- Isolation complète avec l’instance ADEUS (autre IP)

---

## Étapes de déploiement

### 1. Créer l’enregistrement DNS chez OVH

Dans la zone DNS de `public-ia.fr` :
- Type : `A`
- Nom : `aula-experimentation`
- Cible : `51.159.184.221`

---

### 2. Installer Nginx sur l’instance Scaleway

```bash
sudo apt update && sudo apt install nginx -y

sudo nano /etc/nginx/sites-available/aula

'''contenu

### 3. Créer le fichier de configuration Nginx
bash

sudo nano /etc/nginx/sites-available/aula

Contenu :

nginx
server {
    listen 80;
    server_name aula-experimentation.public-ia.fr;

    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

### 4. Activer le site et redémarrer Nginx
bash

sudo ln -s /etc/nginx/sites-available/aula /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx

### 5. Installer Certbot et activer HTTPS
bash

sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d aula-experimentation.public-ia.fr

Certbot configure automatiquement le HTTPS avec redirection du HTTP.
