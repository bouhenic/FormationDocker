 🐳 TP Docker 1

Ce TP a pour objectif de comprendre progressivement comment fonctionne Docker :
- lancement d’un conteneur Apache/PHP et création d'une page web
- persistance des données avec **volumes**
- développement local avec **bind mounts**

---

## 🧪 Partie 1 — Conteneur Docker simple avec Apache/PHP

### 🎯 Objectifs
- Lancer un conteneur en tâche de fond
- Obtenir un terminal dans le conteneur
- Créer une page PHP
- Accéder à la page via le navigateur
- Identifier le réseau Docker utilisé


### 1️⃣ Lancer un conteneur Apache/PHP en arrière-plan
```bash
docker run -d --name mon-php -p 8080:80 php:8.2-apache
```

📌 *`mon-php` est le nom du conteneur — pratique pour s’y référer ensuite.*

---

### 2️⃣ Ouvrir un shell dans le conteneur
```bash
docker exec -it mon-php bash
```

> *Alternative :*
```bash
docker run -it --name mon-php -p 8080:80 php:8.2-apache bash
```

---

### 3️⃣ Créer une page PHP

Dans le conteneur :
```bash
apt update
apt install -y nano iproute2
nano /var/www/html/index.php
```

Y coller :
```php
<?php
echo '<h1>Bonjour depuis Docker 🐳</h1>';
echo '<p>PHP fonctionne dans le conteneur !</p>';
?>
```

Recharger Apache sans quitter Bash :
```bash
apache2ctl -k graceful
```

---

### 4️⃣ Tester l'application dans le navigateur
👉 http://localhost:8080

> On accède au **port 80** du conteneur via le **port 8080** de l’hôte : `-p 8080:80`

---

### 5️⃣ Identifier le réseau utilisé

Dans le conteneur :
```bash
ip a
```

Extrait :
```
inet 172.17.0.2/16 ...
eth0@if14 ...
```

#### **Preuves que l'on est en réseau `bridge` par défaut**

| Indice | Observation | Signification |
|--------|-------------|---------------|
| Adresse IP | `172.17.x.x` | Plage du réseau Docker bridge |
| Interface | `eth0@if14` | Interface veth connectée à `docker0` |

> Le conteneur est accessible directement :  
➡️ http://172.17.0.2

---

### 6️⃣ Quitter et supprimer le conteneur

Quitter le shell :
```bash
exit
```

Le conteneur tourne toujours :
```bash
docker ps
```

Supprimer :
```bash
docker stop mon-php
docker rm mon-php
```

---

## 📦 Partie 2 — Persistance des données avec un volume Docker

### 🎯 Objectifs
- Stocker les fichiers dans un volume persistant
- Constater que les fichiers restent même si le conteneur disparaît

---

### 1️⃣ Créer un volume
```bash
docker volume create tp-volume
```

---

### 2️⃣ Lancer un conteneur utilisant ce volume
```bash
docker run -it --name mon-php   -p 8080:80   -v tp-volume:/var/www/html   php:8.2-apache bash
```

Créer ensuite `index.php` comme en Partie 1.

Recharger Apache :
```bash
apache2ctl -k graceful
```

Tester :
👉 http://localhost:8080

---

### 3️⃣ Test de persistance

Supprimer le conteneur :
```bash
exit
docker rm -f mon-php
```

Relancer :
```bash
docker run -it --name mon-php   -p 8080:80   -v tp-volume:/var/www/html   php:8.2-apache bash
```

Dans le conteneur :
```bash
ls /var/www/html
```
➡️ **`index.php` est toujours là : preuve de persistance**

---

### 4️⃣ Localiser le volume sur l'hôte

```bash
docker volume inspect tp-volume
```

Exemple :
```
"Mountpoint": "/var/lib/docker/volumes/tp-volume/_data"
```

Afficher le contenu depuis l’hôte :
```bash
sudo ls /var/lib/docker/volumes/tp-volume/_data
```

---

### 5️⃣ Supprimer le volume

⚠️ seulement si aucun conteneur ne l’utilise

```bash
docker volume rm tp-volume
```

---

## 🪟 Partie 3 — Développement local avec Bind Mount

### 🎯 Objectifs
- Modifier les fichiers PHP depuis l’hôte
- Voir les changements en direct dans le conteneur

---

### 1️⃣ Créer un dossier de travail sur l’hôte
```bash
mkdir tp-docker-site
cd tp-docker-site
echo "<?php echo '<h1>Bonjour depuis un bind mount</h1>'; ?>" > index.php
```

---

### 2️⃣ Lancer un conteneur avec un bind mount

```bash
docker run -it --name mon-php   -p 8080:80   -v "$(pwd)":/var/www/html   php:8.2-apache bash
```

> **Ici le dossier local est monté directement dans le conteneur.**

---

### 3️⃣ Tester

Modifier `index.php` **sur votre machine**  
puis recharger :
```bash
apache2ctl -k graceful
```

Accéder :
👉 http://localhost:8080

➡️ **Les changements sont immédiatement visibles**

---

## 🎓 Résumé général

| Méthode | Persistance | Modif depuis l’hôte | Cas d’usage |
|---------|-------------|---------------------|--------------|
| conteneur seul | ❌ non | ❌ non | tests |
| volume | ✔️ oui | ❌ non | stockage |
| bind mount | ✔️ oui | ✔️ oui | développement local |

---

## 🏁 Fin du TP

Bravo ! Vous savez maintenant :  
✔ lancer un conteneur Apache/PHP  
✔ accéder via un navigateur  
✔ conserver des fichiers via un volume  
✔ développer via un bind mount

---

### 📌 Commandes utiles

```bash
docker ps        # conteneurs actifs
docker ps -a     # tous les conteneurs
docker stop NAME
docker rm NAME
docker volume ls
docker volume rm NAME
```

---
