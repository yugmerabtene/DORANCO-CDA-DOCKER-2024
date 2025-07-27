**MODULE 8 – PROJET FINAL : APPLICATION CONTENEURISÉE CI/CD**

---

### 1. Objectifs pédagogiques

À l’issue de ce module, l’apprenant sera capable de :

* Conteneuriser une application Java Spring Boot avec base PostgreSQL
* Orchestrer les services avec Docker Compose
* Intégrer une pipeline CI/CD (tests, build, push, déploiement)
* Déployer automatiquement sur un serveur distant ou en local

---

### 2. Cahier des charges

**Nom du projet :** `springboot-notes-app`

**Fonctionnalités :**

* API REST pour gérer des notes (CRUD)
* Base PostgreSQL
* Build avec Maven
* Tests unitaires avec JUnit
* Dockerisation complète
* Pipeline GitLab CI ou GitHub Actions
* Déploiement automatique sur serveur VPS via SSH

---

### 3. Structure du projet

```
springboot-notes-app/
├── src/
├── pom.xml
├── Dockerfile
├── docker-compose.yml
├── .env
├── .gitlab-ci.yml (ou .github/workflows/ci.yml)
```

---

### 4. Dockerfile (multistage)

```Dockerfile
FROM maven:3.9.4-eclipse-temurin-17 AS builder
WORKDIR /build
COPY . .
RUN mvn clean package -DskipTests

FROM openjdk:17-jdk-slim
WORKDIR /app
COPY --from=builder /build/target/*.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

### 5. docker-compose.yml

```yaml
version: '3.8'

services:
  db:
    image: postgres:15
    restart: always
    environment:
      POSTGRES_USER: ${DB_USER}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_DB: ${DB_NAME}
    volumes:
      - dbdata:/var/lib/postgresql/data

  app:
    build: .
    ports:
      - "8080:8080"
    depends_on:
      - db
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://db:5432/${DB_NAME}
      SPRING_DATASOURCE_USERNAME: ${DB_USER}
      SPRING_DATASOURCE_PASSWORD: ${DB_PASSWORD}

volumes:
  dbdata:
```

---

### 6. .env

```
DB_USER=notesuser
DB_PASSWORD=secretpass
DB_NAME=notesdb
```

---

### 7. GitLab CI : .gitlab-ci.yml

```yaml
stages:
  - test
  - build
  - deploy

variables:
  IMAGE_TAG: $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA

test:
  stage: test
  image: maven:3.9-eclipse-temurin-17
  script:
    - mvn clean verify

build:
  stage: build
  image: docker:24
  services:
    - docker:24-dind
  script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG
  needs:
    - test

deploy:
  stage: deploy
  image: alpine
  before_script:
    - apk add --no-cache openssh
  script:
    - ssh user@ip "
        docker pull $IMAGE_TAG &&
        docker stop notesapp || true &&
        docker rm notesapp || true &&
        docker run -d -p 8080:8080 --name notesapp $IMAGE_TAG
      "
  when: manual
```

---

### 8. GitHub Actions : .github/workflows/ci.yml

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up JDK
        uses: actions/setup-java@v3
        with:
          java-version: '17'
          distribution: 'temurin'
      - run: mvn clean verify

  build-and-push:
    runs-on: ubuntu-latest
    needs: test
    steps:
      - uses: actions/checkout@v3
      - name: Docker login
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKERHUB_USER }}
          password: ${{ secrets.DOCKERHUB_PASS }}
      - name: Build and push
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: youruser/springboot-notes:latest
```

---

### 9. Tests unitaires obligatoires

Dans le projet Java :

```java
@Test
public void testCreateNote() {
    Note note = new Note("Test", "Contenu");
    assertEquals("Test", note.getTitle());
}
```

---

## FICHE LIVRABLE DU PROJET FINAL

**À fournir :**

1. Code source du projet complet (`springboot-notes-app.zip`)
2. Fichier `Dockerfile` fonctionnel
3. `docker-compose.yml` + `.env`
4. Pipeline CI/CD (`.gitlab-ci.yml` ou `.github/workflows/ci.yml`)
5. Exemple de test unitaire
6. Capture d’écran du pipeline réussi
7. URL ou capture du déploiement distant
8. Documentation d’installation et exécution

---

## CORRIGÉ ATTENDU

* L’application doit démarrer en local via `docker compose up`
* La base PostgreSQL doit être accessible et utilisée par l’API
* La pipeline CI/CD doit valider les tests, builder l’image et la publier
* Le déploiement sur un serveur distant (facultatif si en local) doit relancer l’app à chaque push
