**MODULE 7 – DOCKER ET CI/CD (AVEC PHASE DE TESTS)**

---

### 1. Objectifs pédagogiques

À l’issue de ce module, l’apprenant sera capable de :

* Structurer un pipeline CI/CD Docker avec **tests, build, push et déploiement**
* Utiliser **GitLab CI** et **GitHub Actions** pour intégrer cette chaîne
* Automatiser la **validation** du code avant construction de l’image
* Déployer l’image Docker sur un serveur distant après validation

---

### 2. Phases typiques d’un pipeline Docker CI/CD

1. **Tests** (unitaires, lint, sécurité)
2. **Build** de l’image Docker
3. **Push** vers le registre
4. **Déploiement** automatique

---

### 3. Exemple GitLab CI avec tests Java (Maven) + build Docker

#### `.gitlab-ci.yml`

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
  image: docker:24.0.2
  services:
    - docker:24.0.2-dind
  script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG
  needs:
    - job: test
      artifacts: true

deploy:
  stage: deploy
  image: alpine
  before_script:
    - apk add --no-cache openssh
  script:
    - ssh user@ip-server "
        docker pull $IMAGE_TAG &&
        docker stop app || true &&
        docker rm app || true &&
        docker run -d -p 80:80 --name app $IMAGE_TAG
      "
  when: manual
```

**Explication :**

* La phase `test` utilise Maven pour exécuter les tests unitaires
* La phase `build` ne s’exécute que si les tests réussissent
* Le `deploy` est déclenché manuellement

---

### 4. Exemple GitHub Actions : test Maven + build/push Docker

```yaml
name: CI/CD Docker Java

on:
  push:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - name: Test with Maven
        uses: actions/setup-java@v3
        with:
          distribution: 'temurin'
          java-version: '17'
      - run: mvn clean verify

  build-and-push:
    runs-on: ubuntu-latest
    needs: test
    steps:
      - name: Checkout
        uses: actions/checkout@v3
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
          tags: youruser/yourimage:latest
```

---

## FICHES TP CORRIGÉES – CI/CD AVEC PHASE DE TEST

---

### TP 1 – CI GitLab avec tests Maven + build + push

**Objectif :** Construire un pipeline complet avec tests avant build

**Étapes :**

1. Ajouter un fichier `.gitlab-ci.yml` avec les trois étapes
2. Ajouter un test unitaire dans votre projet Java (ex : JUnit)

**Commande locale de test :**

```bash
mvn clean verify
```

**Résultat attendu sur GitLab :**

* Phase `test` passe ✅
* Phase `build` passe ✅
* L’image est poussée automatiquement dans le registry GitLab

---

### TP 2 – GitHub Actions avec phase de tests + push Docker

**Objectif :** Vérifier que les tests passent avant le build et push

**Étapes :**

1. Ajouter fichier `.github/workflows/ci.yml`
2. Configurer vos secrets : `DOCKERHUB_USER`, `DOCKERHUB_PASS`

**Test :**

* Pousser sur `main`

**Résultat attendu :**

* Tests Maven lancés
* Build et push déclenchés uniquement si les tests sont OK

---

### TP 3 – Déploiement distant manuel après tests (GitLab ou GitHub)

**Objectif :** Déployer manuellement une image Docker validée

**Commandes manuelles (testables localement ou en SSH) :**

```bash
docker pull youruser/image:tag
docker stop app || true
docker rm app || true
docker run -d -p 80:80 --name app youruser/image:tag
```

**Résultat attendu :**

* L’application est relancée sur le serveur avec la nouvelle image
