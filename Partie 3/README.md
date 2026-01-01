
# 🐳 TP Docker n°3 — Déployer PHP + MySQL avec docker-compose

Ce TP poursuit la découverte de Docker en utilisant **docker-compose** pour déployer une petite application composée de deux services :

- un serveur **Apache/PHP**
- une base de données **MySQL**

⚠️ L’image officielle `php:8.2-apache` **ne contient pas l’extension `mysqli` par défaut**.  
Nous allons donc construire **notre propre image** à partir de `php:8.2-apache`, y ajouter `mysqli` **et l’outil `ping`** pour tester la communication réseau entre conteneurs.

---

## 🎯 Objectifs

- Découvrir et utiliser **docker-compose**
- Automatiser le lancement d'une application **PHP + MySQL**
- Comprendre **bind-mount vs volume**
- Persister les données d'une base MySQL
- **Inspecter un volume Docker** et visualiser les fichiers MySQL
- Comprendre la différence entre `docker compose down` et `docker compose down --volumes`
- Tester la communication réseau entre conteneurs avec **`ping`**

---

## 📂 Partie 1 — Préparer le projet

```bash
mkdir tp-docker-compose
cd tp-docker-compose
```

Arborescence visée :

```
tp-docker-compose
 ├── docker-compose.yml
 ├── Dockerfile
 └── index.php
```

---

## 📝 Partie 2 — Créer la page `index.php`

Créer le fichier :

```bash
nano index.php
```

Remplir :

```php
<?php
$host = "db";
$user = "root";
$pass = "rootpass";
$dbname = "demo";

$conn = new mysqli($host, $user, $pass, $dbname);

if ($conn->connect_error) {
    die("<h1>Connexion MySQL échouée 😢</h1>" . $conn->connect_error);
}

echo "<h1>Connexion MySQL réussie 🎉</h1>";

$result = $conn->query("SELECT 'Hello depuis MySQL 🐬' AS message;");
$row = $result->fetch_assoc();
echo "<p>" . $row["message"] . "</p>";

$conn->close();
?>
```

---

## 🧱 Partie 3 — Créer le `Dockerfile` (PHP + mysqli + ping)

Créer le fichier :

```bash
nano Dockerfile
```

Y coller :

```dockerfile
FROM php:8.2-apache

# Installer ping pour tester la communication réseau entre services
RUN apt update && apt install -y iputils-ping

# Installer les extensions nécessaires pour MySQL
RUN docker-php-ext-install mysqli pdo_mysql
```

📌 Remarque :  
L’outil `ping` sera disponible dans le conteneur `php-web`, ce qui permettra d’utiliser la commande :
```bash
ping -c 2 db
```

---

## 🏗️ Partie 4 — Créer le fichier `docker-compose.yml` (sans `version:`)

Créer le fichier :

```bash
nano docker-compose.yml
```

Contenu :

```yaml
services:
  web:
    build: .
    container_name: php-web
    ports:
      - "8080:80"
    volumes:
      - .:/var/www/html
    depends_on:
      - db
    networks:
      - tpnet

  db:
    image: mysql:8.0
    container_name: mysql-db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
      MYSQL_DATABASE: demo
    volumes:
      - dbdata:/var/lib/mysql
    networks:
      - tpnet

networks:
  tpnet:

volumes:
  dbdata:
```

📌 Remarque :  
Avec Docker Compose v2, la clé `version:` est **obsolète** — elle n’est plus utilisée.

---

## 🚀 Partie 5 — Construire & lancer les services

```bash
docker compose build
docker compose up -d
docker compose ps
```

Les conteneurs `php-web` et `mysql-db` doivent être **Up**.

---

## 🌐 Partie 6 — Tester dans le navigateur

Ouvrir dans un navigateur :

```
http://localhost:8080
```

Résultat attendu :

- `Connexion MySQL réussie 🎉`
- `Hello depuis MySQL 🐬`

Si ce n’est pas le cas, vérifier :
- l’état des conteneurs avec `docker compose ps`
- que l’image a bien été reconstruite après création/modification du `Dockerfile` (`docker compose build`)

---

## 🔍 Partie 7 — Tester la communication réseau avec `ping`

Entrer dans le conteneur `web` :

```bash
docker compose exec web bash
```

Puis exécuter :

```bash
ping -c 2 db
```

Vous devez voir des réponses, par exemple :

```text
PING db (172.18.0.3): 56 data bytes
64 bytes from 172.18.0.3: icmp_seq=1 ttl=64 time=0.10 ms
64 bytes from 172.18.0.3: icmp_seq=2 ttl=64 time=0.09 ms

--- db ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
```

💡 Conclusion :  
Le conteneur `php-web` peut joindre le conteneur `mysql-db` via le nom `db` sur le réseau `tpnet`.

---

## 🧱 Partie 8 — **Inspecter le volume** (avant suppression !)

Lister les volumes :

```bash
docker volume ls
```

Identifier celui de votre projet, par exemple :

```text
local   tp-docker-compose_dbdata
```

> Docker Compose **préfixe** le nom logique `dbdata` par le nom du projet (`tp-docker-compose`).

Inspecter le volume :

```bash
docker volume inspect tp-docker-compose_dbdata
```

Repérer le champ `Mountpoint`, par exemple :

```json
"Mountpoint": "/var/lib/docker/volumes/tp-docker-compose_dbdata/_data"
```

Lister le contenu du volume (sur la machine hôte) :

```bash
sudo ls /var/lib/docker/volumes/tp-docker-compose_dbdata/_data
```

Vous devriez voir (entre autres) :

- la base `demo/`
- `mysql/`, `performance_schema/`, `sys/`
- des fichiers internes (`ibdata1`, `ibtmp1`, `undo_001`, `binlog.000001`, etc.)

💡 Message important :  
Ces fichiers sont **gérés par MySQL**. On peut les observer, mais **on ne les modifie pas manuellement**.  
Pour agir sur les données, on utilise des requêtes SQL (client MySQL, PHP, etc.).

---

## ♻️ Partie 9 — Tester la persistance du volume

Arrêter les conteneurs **sans supprimer les volumes** :

```bash
docker compose down
```

Puis relancer :

```bash
docker compose up -d
```

Les données MySQL sont **conservées** (le volume `dbdata` n’a pas été supprimé).

---

## 🗑️ Partie 10 — Supprimer totalement (conteneurs + volume)

⚠️ Ne faire qu’après l’inspection du volume !

```bash
docker compose down --volumes
```

Puis vérifier :

```bash
docker volume ls
```

Le volume `tp-docker-compose_dbdata` a disparu : les données MySQL sont définitivement supprimées.

---


Bravo 🎉 Bon TP 🚀
