
# 🐳 TP Docker n°2 — Créer une image personnalisée avec Dockerfile

Ce TP poursuit la découverte de Docker en créant **notre propre image**, basée sur l’image officielle `php:8.2-apache`, afin de retrouver le fonctionnement du conteneur du TP1 mais cette fois de manière **reproductible** grâce à un Dockerfile.

---

## 🎯 Objectifs

- Comprendre le rôle d’un **Dockerfile**
- Construire une image personnalisée basée sur `php:8.2-apache`
- Ajouter des outils utiles (`nano`, `iproute2`)
- Copier une page `index.php` dans l’image
- Lancer un conteneur basé sur cette image
- Reconstruire l’image pour mettre à jour l’application

---

## 📂 Partie 1 — Préparer le projet

Sur votre machine hôte :

```bash
mkdir tp-docker-dockerfile
cd tp-docker-dockerfile
```

Créer le fichier `index.php` à la racine :

```bash
echo "<?php echo '<h1>Bonjour depuis mon image Docker personnalisée 🐳</h1>'; ?>" > index.php
```

Vous devez maintenant avoir :

```
tp-docker-dockerfile
 └── index.php
```

---

## 🏗️ Partie 2 — Écrire le Dockerfile

Créer un fichier nommé `Dockerfile` :

```bash
nano Dockerfile
```

Copier le contenu suivant :

```dockerfile
# Image de base avec Apache + PHP
FROM php:8.2-apache

# Installer des outils utiles
RUN apt update && apt install -y nano iproute2

# Copier la page PHP dans le dossier web d'Apache
COPY index.php /var/www/html/index.php

# Lancer Apache au premier plan (nécessaire dans Docker)
CMD ["apache2ctl", "-D", "FOREGROUND"]
```

📌 **Analyse des instructions**

| Instruction | Rôle |
|------------|------|
| `FROM` | Choisit l’image de départ |
| `RUN` | Exécute des commandes lors de la construction |
| `COPY` | Ajoute des fichiers dans l’image |
| `CMD` | Commande exécutée au lancement du conteneur |

---

## 🧱 Partie 3 — Construire l’image

Dans le même dossier que le Dockerfile :

```bash
docker build -t mon-php-image .
```

Si tout se passe bien :
```
Successfully tagged mon-php-image:latest
```

---

## 🚀 Partie 4 — Lancer un conteneur basé sur l’image

```bash
docker run -d --name mon-php -p 8080:80 mon-php-image
```

Ouvrir le navigateur :

```
http://localhost:8080
```

➡️ Vous devez voir le message défini dans `index.php`

---

## 🧐 Partie 5 — Vérifications utiles

### Conteneurs en cours
```bash
docker ps
```

### Contenu du dossier web dans le conteneur
```bash
docker exec -it mon-php ls /var/www/html
```

### Adresse IP du conteneur (réseau bridge)
```bash
docker exec -it mon-php ip a
```

---

## 🔁 Partie 6 — Mettre à jour l’application

Modifier `index.php` sur l’hôte :

```bash
echo "<?php echo '<h1>Version 2 🚀</h1>'; ?>" > index.php
```

Reconstruire l’image :

```bash
docker build -t mon-php-image .
```

Redémarrer avec la nouvelle image :

```bash
docker rm -f mon-php
docker run -d --name mon-php -p 8080:80 mon-php-image
```

➡️ Consultez `http://localhost:8080` pour vérifier la mise à jour

---

## 🧹 Partie 7 — Nettoyage

```bash
docker stop mon-php
docker rm mon-php
docker image rm mon-php-image
```

---

## 🎓 Récapitulatif des notions

| Notion | Exemple | Description |
|--------|---------|-------------|
| Dockerfile | `FROM php:8.2-apache` | Recette de création de l’image |
| Image | `mon-php-image` | Contient les fichiers et programmes |
| Conteneur | `mon-php` | Instance en exécution de l’image |
| Construction | `docker build` | Crée une image à partir du Dockerfile |
| Lancement | `docker run` | Lance un conteneur depuis une image |

> **Un conteneur est éphémère. Une image est durable. Le Dockerfile est la recette.**

---

### ✔️ Travail effectué ?

- [ ] Le Dockerfile est créé
- [ ] L’image est construite
- [ ] Le conteneur affiche la page PHP
- [ ] Une modification nécessite une reconstruction
- [ ] Je comprends la différence image / conteneur / Dockerfile
