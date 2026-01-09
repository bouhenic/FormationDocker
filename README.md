# Formation Docker

Ce dépôt propose une **formation progressive à Docker**, construite autour de **travaux pratiques guidés**, destinée :

- aux **étudiants de BTS CIEL**  
  (Cybersécurité, Informatique et Réseaux, Électronique)
- aux **enseignants en formation continue**, souhaitant découvrir Docker et disposer de supports pédagogiques réutilisables.

La formation est volontairement **progressive**, **concrète** et **accessible aux débutants**, sans approche DevOps complexe.

---

## Public visé

Cette formation s’adresse :

- aux **étudiants de BTS CIEL**
- aux **enseignants en formation continue**, désireux de :
  - comprendre les bases de Docker
  - mettre en œuvre des TPs de conteneurisation
  - actualiser leurs pratiques pédagogiques (virtualisation légère, déploiement applicatif)

Elle est conçue pour être :
- sans prérequis Docker
- directement exploitable en contexte pédagogique

---

## Prérequis

### Étudiants BTS CIEL
- Utilisation basique d’un terminal Linux
- Notions de réseau (HTTP, ports)
- Bases en programmation ou script (PHP ou équivalent)

### Enseignants en formation continue
- Connaissances générales en informatique et réseaux
- Aucun prérequis Docker ou DevOps

---

## Objectifs pédagogiques globaux

À l’issue de la formation, l’apprenant (étudiant ou enseignant) sera capable de :

- comprendre les principes fondamentaux de la conteneurisation
- manipuler Docker en ligne de commande
- différencier image, conteneur, volume et bind mount
- créer une image Docker à l’aide d’un Dockerfile
- déployer une application multi-services avec docker-compose
- comprendre la persistance des données dans Docker
- diagnostiquer des erreurs simples (ports, volumes, dépendances)

---

## Contenu du dépôt

- `Partie1/` : Découverte de Docker (conteneurs, ports, volumes)
- `Partie 2/` : Création d’images avec Dockerfile
- `Partie 3/` : Orchestration avec docker-compose (PHP + MySQL)
- `TP_interactif.pdf` : support pédagogique complémentaire

Chaque dossier contient un `README.md` décrivant le TP associé.

---

## Partie 1 — Découverte de Docker

Dossier : `Partie1/`

### Objectifs pédagogiques
- Comprendre ce qu’est un conteneur Docker
- Lancer un serveur web Apache/PHP
- Accéder à un service via un port exposé
- Utiliser un volume Docker
- Comprendre la différence entre volume et bind mount

### Compétences travaillées
- Ligne de commande
- Architecture client / serveur
- Virtualisation légère
- Persistance des données

### Critères de réussite
✔ Le serveur web est accessible via un navigateur  
✔ Les modifications de fichiers sont visibles sans rebuild  
✔ L’étudiant sait expliquer le rôle d’un volume  

---

## Partie 2 — Création d’images avec Dockerfile

Dossier : `Partie 2/`

### Objectifs pédagogiques
- Comprendre le rôle d’un Dockerfile
- Construire une image Docker personnalisée
- Installer des paquets supplémentaires
- Copier des fichiers dans une image
- Rebuilder une image après modification

### Compétences travaillées
- Écriture et lecture de Dockerfile
- Notion de couches (layers)
- Reproductibilité d’un environnement
- Automatisation du déploiement

### Critères de réussite
✔ L’image se build sans erreur  
✔ Le conteneur utilise bien l’image créée  
✔ La page PHP personnalisée s’affiche  
✔ L’étudiant sait expliquer chaque instruction du Dockerfile  

---

## Partie 3 — Orchestration avec docker-compose

Dossier : `Partie 3/`

### Objectifs pédagogiques
- Déployer plusieurs services avec docker-compose
- Comprendre les réseaux Docker
- Mettre en place une base MySQL
- Gérer la persistance des données
- Comprendre l’impact de `down` et `down --volumes`

### Compétences travaillées
- Architecture applicative multi-tiers
- Réseau entre conteneurs
- Variables d’environnement
- Diagnostic applicatif

### Critères de réussite
✔ L’application PHP communique avec MySQL  
✔ Les données persistent après redémarrage  
✔ L’étudiant sait expliquer l’architecture complète  
✔ L’impact de la suppression des volumes est compris  

---

## Spécificités – Formation continue d’enseignants

Cette formation permet aux enseignants de :

- découvrir Docker sans approche complexe
- disposer de TPs immédiatement exploitables
- adapter les exercices à différents niveaux (BTS, BUT, Bac+1)
- faire le lien avec :
  - virtualisation (VM vs conteneur)
  - cybersécurité (isolation, surface d’attaque)
  - déploiement applicatif

Chaque partie peut être utilisée :
- comme TP clé en main
- comme base à adapter ou enrichir

---

## Modalités pédagogiques possibles

- TP guidé pas à pas
- Travail en binôme
- Classe inversée
- Démonstration + autonomie progressive

### Évaluation possible
- QCM de compréhension
- Schéma d’architecture Docker annoté
- Questions orales :
  - image vs conteneur
  - volume vs bind mount
  - Dockerfile vs docker-compose

---

## Support complémentaire

**TP_interactif.pdf**

Utilisable :
- en autonomie
- en formation continue
- comme support de remédiation

---

## Remarque pédagogique

Ce dépôt est volontairement :

- simple
- progressif
- centré sur la compréhension

Il constitue une **base solide** avant d’aborder :
- Docker multi-stage
- sécurité des conteneurs
- CI/CD
- Kubernetes


