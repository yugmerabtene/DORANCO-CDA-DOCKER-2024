# SOMMAIRE

1. Création du projet Spring Boot
2. Implémentation de l’API calculatrice
3. Écriture des tests unitaires
4. Dockerisation du projet
5. Initialisation Git et création des branches
6. Mise en place de la protection de la branche `main`
7. Création des workflows CI/CD
8. Configuration du VPS
9. Paramétrage des secrets GitHub
10. Bonnes pratiques supplémentaires
11. Résumé du cycle de développement

---

## 1. Création du projet Spring Boot

Utiliser Spring Initializr ([https://start.spring.io](https://start.spring.io)) avec les paramètres suivants :

* Project : Maven
* Language : Java
* Java version : 17
* Group : `com.example`
* Artifact : `calculatrice`
* Packaging : Jar
* Dependencies :

  * Spring Web
  * Spring Boot DevTools
  * Spring Boot Starter Test

Décompresser l’archive et ouvrir le projet dans IntelliJ IDEA ou un autre IDE.

---

## 2. Implémentation de l’API

### CalculatriceApplication.java

```java
@SpringBootApplication
public class CalculatriceApplication {
    public static void main(String[] args) {
        SpringApplication.run(CalculatriceApplication.class, args);
    }
}
```

### CalculatriceController.java

```java
@RestController
@RequestMapping("/api")
public class CalculatriceController {
    private final CalculatriceService service;

    public CalculatriceController(CalculatriceService service) {
        this.service = service;
    }

    @GetMapping("/add")
    public double add(@RequestParam double a, @RequestParam double b) {
        return service.add(a, b);
    }

    @GetMapping("/sub")
    public double sub(@RequestParam double a, @RequestParam double b) {
        return service.sub(a, b);
    }

    @GetMapping("/mul")
    public double mul(@RequestParam double a, @RequestParam double b) {
        return service.mul(a, b);
    }

    @GetMapping("/div")
    public double div(@RequestParam double a, @RequestParam double b) {
        return service.div(a, b);
    }

    @GetMapping("/health")
    public String health() {
        return "UP";
    }
}
```

### CalculatriceService.java

```java
@Service
public class CalculatriceService {
    private static final Logger logger = LoggerFactory.getLogger(CalculatriceService.class);

    public double add(double a, double b) {
        logger.info("Addition: {} + {}", a, b);
        return a + b;
    }

    public double sub(double a, double b) {
        logger.info("Soustraction: {} - {}", a, b);
        return a - b;
    }

    public double mul(double a, double b) {
        logger.info("Multiplication: {} * {}", a, b);
        return a * b;
    }

    public double div(double a, double b) {
        if (b == 0) throw new ArithmeticException("Division par zéro");
        logger.info("Division: {} / {}", a, b);
        return a / b;
    }
}
```

---

## 3. Tests unitaires

### CalculatriceServiceTest.java

```java
class CalculatriceServiceTest {
    private final CalculatriceService service = new CalculatriceService();

    @Test
    void testAdd() {
        assertEquals(5.0, service.add(2, 3));
    }

    @Test
    void testSub() {
        assertEquals(1.0, service.sub(5, 4));
    }

    @Test
    void testMul() {
        assertEquals(6.0, service.mul(2, 3));
    }

    @Test
    void testDiv() {
        assertEquals(2.0, service.div(6, 3));
    }

    @Test
    void testDivByZero() {
        assertThrows(ArithmeticException.class, () -> service.div(1, 0));
    }
}
```

---

## 4. Dockerisation

### Dockerfile

```dockerfile
FROM maven:3.9.6-eclipse-temurin-17 as builder
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests=false

FROM eclipse-temurin:17
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8080
CMD ["java", "-jar", "app.jar"]
```

### .dockerignore

```
target/
.git/
.idea/
*.iml
```

---

## 5. Git et gestion des branches

```bash
git init
git checkout -b dev
git add .
git commit -m "Initial commit"
git remote add origin git@github.com:<utilisateur>/calculatrice.git
git push -u origin dev
```

Créer ensuite la branche `main` sur GitHub (vide), puis ouvrir une Pull Request de `dev` vers `main`.

---

## 6. Protection de la branche `main`

Sur GitHub > Settings > Branches :

* Créer une règle de branche `main`
* Cochez :

  * Require pull request before merging
  * Require status checks to pass
  * Require linear history (optionnel)

---

## 7. Workflows GitHub Actions

### build.yml

```yaml
name: Build & Test

on:
  pull_request:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-java@v3
        with:
          distribution: 'temurin'
          java-version: '17'

      - name: Build and test
        run: mvn clean verify
```

### deploy.yml

```yaml
name: Deploy to VPS

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-java@v3
        with:
          distribution: 'temurin'
          java-version: '17'

      - name: Build and test
        run: mvn clean verify

      - name: Docker build
        run: |
          TAG=$(date +%Y%m%d%H%M%S)
          docker build -t calc-spring:$TAG .
          echo "TAG=$TAG" >> $GITHUB_ENV

      - name: Save Docker image
        run: docker save -o calc-spring.tar calc-spring:$TAG

      - name: Upload image
        uses: appleboy/scp-action@master
        with:
          host: ${{ secrets.VPS_HOST }}
          username: ${{ secrets.VPS_USER }}
          key: ${{ secrets.VPS_SSH_KEY }}
          source: "calc-spring.tar"
          target: "/home/${{ secrets.VPS_USER }}/"

      - name: Deploy
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.VPS_HOST }}
          username: ${{ secrets.VPS_USER }}
          key: ${{ secrets.VPS_SSH_KEY }}
          script: |
            docker load -i calc-spring.tar
            docker stop calc || true
            docker rm calc || true
            docker run -d --name calc -p 80:8080 calc-spring:$TAG
            docker image prune -af --filter "until=48h"
```

---

## 8. Configuration du VPS

```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl enable docker
sudo systemctl start docker
```

Créer une clé SSH dédiée, ajouter la clé publique à `~/.ssh/authorized_keys` sur le VPS.

---

## 9. Secrets GitHub à créer

* `VPS_HOST` : IP du VPS
* `VPS_USER` : Nom de l’utilisateur SSH
* `VPS_SSH_KEY` : Clé privée SSH au format texte brut

---

## 10. Bonnes pratiques supplémentaires

* Ajoutez `checkstyle` dans `build.yml` pour le lint
* Ajoutez un endpoint `/health` pour la supervision
* Versionnez vos images Docker (exemple avec `TAG=$(date +%Y%m%d%H%M%S)`)
* Supprimez les anciennes images (`docker image prune`)
* Testez les cas limites (`null`, très grands nombres, division par zéro)
* Activez la journalisation avec SLF4J (déjà intégré ci-dessus)

---

## 11. Résumé du cycle de développement

1. Développer sur la branche `dev`
2. Ouvrir une Pull Request vers `main`
3. GitHub Actions lance les tests (`build.yml`)
4. Si les tests passent, le merge est autorisé
5. Merge vers `main` déclenche le déploiement automatique (`deploy.yml`)
6. Le VPS télécharge et lance la nouvelle version via Docker
