**MODULE 3 – CRÉATION ET GESTION DES IMAGES DOCKER**

---

### 1. Objectifs pédagogiques

À l’issue de ce module, l’apprenant sera capable de :

* Comprendre la structure d’une image Docker
* Créer une image personnalisée à partir d’un Dockerfile
* Optimiser une image Docker
* Publier une image dans un registre (Docker Hub ou privé)

---

### 2. Rappels fondamentaux

Une image Docker est un **modèle figé** contenant :

* Un système minimal (souvent basé sur Alpine, Debian, Ubuntu, etc.)
* Une application ou un service (par exemple une API Java)
* Ses dépendances, scripts, configurations

Un **conteneur** est une instance en cours d’exécution d’une image.

---

### 3. Dockerfile : structure et instructions de base

Un **Dockerfile** est un fichier texte sans extension nommé `Dockerfile`.

#### Exemple simple pour une application Java :

```
FROM openjdk:17-jdk-slim
WORKDIR /app
COPY target/monapp.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```

#### Explication des instructions :

* `FROM` : image de base
* `WORKDIR` : dossier de travail interne
* `COPY` : copie de fichiers locaux vers l’image
* `ENTRYPOINT` : commande exécutée au lancement du conteneur

---

### 4. Construction et gestion des images

#### 4.1. Construire une image :

**Commandes identiques Linux / macOS / Windows (PowerShell ou WSL) :**

Depuis le dossier contenant votre `Dockerfile` :

```
docker build -t monimage:1.0 .
```

* `-t` : nommage (`monimage`) et version (`1.0`)
* `.` : contexte de build (répertoire courant)

#### 4.2. Lister les images :

```
docker images
```

#### 4.3. Supprimer une image :

```
docker rmi monimage:1.0
```

---

### 5. Optimisation d’image Docker

#### Recommandations :

* Utiliser une image de base légère (Alpine, Slim)
* Limiter le nombre de couches (`RUN` combinés)
* Supprimer les fichiers inutiles après installation
* Utiliser le **multi-stage build** si compilation requise

#### Exemple de multi-stage pour une app Java Maven :

```
FROM maven:3.9.4-eclipse-temurin-17 AS builder
WORKDIR /build
COPY . .
RUN mvn clean package -DskipTests

FROM openjdk:17-jdk-slim
WORKDIR /app
COPY --from=builder /build/target/monapp.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

### 6. Publication d’une image sur Docker Hub

#### 6.1. Se connecter à Docker Hub :

```
docker login
```

#### 6.2. Taguer l’image :

```
docker tag monimage:1.0 monutilisateurdocker/monimage:1.0
```

#### 6.3. Pousser vers Docker Hub :

```
docker push monutilisateurdocker/monimage:1.0
```

---

## FICHES TP CORRIGÉES – MODULE 3 – CRÉATION D’IMAGES

---

### TP 1 – Créer une image Docker simple (Hello World Java)

**Objectif :** Créer une image exécutant une application Java

**Pré-requis :** un fichier `.jar` compilé (`HelloDocker.jar`)

**Fichiers :**

```
Dockerfile
HelloDocker.jar
```

**Contenu du Dockerfile :**

```
FROM openjdk:17-jdk-slim
WORKDIR /app
COPY HelloDocker.jar .
ENTRYPOINT ["java", "-jar", "HelloDocker.jar"]
```

**Commandes multiplateformes :**

```
docker build -t hello-java .
docker run hello-java
```

**Résultat attendu :**

* Le programme affiche "Hello Docker Java" dans la console

---

### TP 2 – Créer une image avec multi-stage Maven

**Objectif :** Compiler une application Maven dans un conteneur

**Structure du projet (à la racine du projet Java Maven) :**

* `pom.xml`
* `src/`

**Dockerfile :**

```
FROM maven:3.9.4-eclipse-temurin-17 AS build
WORKDIR /build
COPY . .
RUN mvn clean package -DskipTests

FROM openjdk:17-jdk-slim
WORKDIR /app
COPY --from=build /build/target/*.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```

**Commandes multiplateformes :**

```
docker build -t monappli-java .
docker run -p 8080:8080 monappli-java
```

**Résultat attendu :**

* L’application Java démarre
* Accessible sur [http://localhost:8080](http://localhost:8080) (selon le projet)

---

### TP 3 – Tagger et publier une image

**Objectif :** Publier une image Docker sur Docker Hub

**Étapes :**

1. Créer un compte sur [https://hub.docker.com](https://hub.docker.com)
2. Se connecter via terminal :

```
docker login
```

3. Tagger l’image :

```
docker tag monappli-java votreuser/monappli-java:latest
```

4. Pousser l’image :

```
docker push votreuser/monappli-java:latest
```

**Résultat attendu :**

* L’image est disponible publiquement ou en privé sur Docker Hub
