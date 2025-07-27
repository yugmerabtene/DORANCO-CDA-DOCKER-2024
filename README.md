**SYLLABUS COMPLET – FORMATION DOCKER ORIENTÉE JAVA**

---

**MODULE 1 – Introduction à Docker et à la conteneurisation**

**Objectifs :**

* Comprendre la différence entre virtualisation et conteneurisation
* Maîtriser les bases de l’architecture Docker

**Contenu :**

1. Virtualisation vs Conteneurisation
2. Avantages de Docker
3. Architecture Docker : daemon, client, registry, image, container
4. Cas d’usage en entreprise

**Travaux pratiques :**

* Installation de Docker sur Linux (Ubuntu/Debian)
* Exécution du conteneur `hello-world`
* Comparaison entre une VM et un conteneur Docker (`alpine`)

---

**MODULE 2 – Manipulation des conteneurs**

**Objectifs :**

* Savoir créer, exécuter, inspecter et supprimer des conteneurs

**Contenu :**

1. Commandes de base : `docker run`, `ps`, `exec`, `logs`, `stop`, `rm`
2. Modes interactifs vs détachés
3. Redirection de ports (`-p`) et montage de volumes (`-v`)
4. Gestion des logs et états (`restart`, `inspect`, `top`, `stats`)

**Travaux pratiques :**

* Déploiement d’un conteneur NGINX
* Connexion shell dans un conteneur
* Mini-labo Apache PHP dans un conteneur pour comparer avec NGINX

---

**MODULE 3 – Création et gestion des images Docker**

**Objectifs :**

* Créer, optimiser et publier des images Docker personnalisées

**Contenu :**

1. Structure d’une image Docker
2. Écriture d’un Dockerfile
3. Optimisations : multi-stage build, réduction de taille
4. Utilisation de DockerHub et d’un registre privé

**Travaux pratiques :**

* Création d’une image Java avec un projet Maven (ex : API REST Spring Boot)
* Optimisation avec multi-stage build (build + runtime)
* Publication sur Docker Hub ou registre privé local

---

**MODULE 4 – Réseaux et volumes dans Docker**

**Objectifs :**

* Utiliser efficacement les volumes et les réseaux Docker

**Contenu :**

1. Types de réseaux : bridge, host, overlay
2. Création et inspection de réseaux personnalisés
3. Volumes : named volumes, bind mounts
4. Utilisation des volumes pour la persistance

**Travaux pratiques :**

* Création d’un réseau personnalisé entre deux conteneurs (ex : front + back)
* Déploiement d’une base de données PostgreSQL avec volume persistant
* Test de communication réseau inter-conteneurs

---

**MODULE 5 – Docker Compose**

**Objectifs :**

* Orchestrer des applications multi-conteneurs

**Contenu :**

1. Présentation de Docker Compose
2. Syntaxe de `docker-compose.yml`
3. Dépendances de services, variables d’environnement, volumes et réseaux
4. Mise à l’échelle (`scale`), redémarrage, logs multi-services

**Travaux pratiques :**

* Déploiement d’une stack Java Spring Boot + PostgreSQL
* Configuration de variables d’environnement dans `docker-compose.yml`
* Déploiement d’un outil open source complet (ex : SonarQube)

---

**MODULE 6 – Sécurité Docker**

**Objectifs :**

* Mettre en œuvre les bonnes pratiques de sécurité avec Docker

**Contenu :**

1. Conteneur non-root, principes de moindre privilège
2. Isolation des conteneurs (namespaces, cgroups)
3. Scans de vulnérabilités (Trivy, Docker Scan)
4. Gestion des secrets avec Docker
5. AppArmor, SELinux, audit des conteneurs

**Travaux pratiques :**

* Scan d’image avec Trivy
* Création d’un conteneur non-root pour une application Java
* Injection de secrets dans une application Spring Boot via volume ou variable d’environnement

---

**MODULE 7 – Docker dans un contexte DevOps**

**Objectifs :**

* Intégrer Docker dans un pipeline de CI/CD

**Contenu :**

1. Introduction à GitLab CI et GitHub Actions avec Docker
2. Création d’une image à partir d’un build Maven
3. Utilisation d’un registre Docker privé (Harbor, GitLab registry)
4. Bonnes pratiques de versionnage et de cache

**Travaux pratiques :**

* Création d’un pipeline CI/CD avec test, build et push d’une image Java
* Intégration d’un scanner de vulnérabilité dans le pipeline
* Déploiement automatique sur un serveur distant via SSH ou Runner GitLab

---

**MODULE 8 – Projet final : application Java conteneurisée**

**Objectifs :**

* Construire une application complète conteneurisée et déployée automatiquement

**Contenu du projet :**

1. Application Java (Spring Boot REST API)
2. Base de données PostgreSQL ou MySQL
3. Interface d’administration (optionnelle)
4. Dockerisation complète : Dockerfile, `docker-compose.yml`
5. Pipeline CI/CD complet (test + build + push + déploiement)
6. Déploiement sur un VPS ou serveur local

**Livrables attendus :**

* Code source versionné (Git)
* Dockerfile et `docker-compose.yml`
* Fichier `.gitlab-ci.yml` ou `.github/workflows`
* Capture du pipeline
* Documentation technique du projet



