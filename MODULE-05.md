**MODULE 5 – DOCKER COMPOSE**

---

### 1. Objectifs pédagogiques

À l’issue de ce module, l’apprenant sera capable de :

* Comprendre la structure et l’intérêt de Docker Compose
* Définir plusieurs services dans un fichier `docker-compose.yml`
* Orchestrer des conteneurs liés (ex : API + base de données)
* Utiliser les variables d’environnement, volumes, réseaux personnalisés

---

### 2. Introduction à Docker Compose

**Docker Compose** permet de :

* Définir plusieurs services dans un fichier unique
* Lancer toute une stack avec une seule commande
* Faciliter le développement multi-conteneurs (API, BDD, interface…)

---

### 3. Installation de Docker Compose

#### Linux (Docker Compose Plugin)

Déjà inclus depuis Docker Engine 20.10.13. Vérifier avec :

```
docker compose version
```

Sinon, installer via :

```
sudo apt install docker-compose-plugin
```

#### Windows / macOS

Docker Compose est inclus dans Docker Desktop.
Vérifier :

```
docker compose version
```

---

### 4. Structure d’un fichier `docker-compose.yml`

Exemple simple : API Java + PostgreSQL

```yaml
version: '3.8'

services:
  db:
    image: postgres:15
    restart: always
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypass
      POSTGRES_DB: mydb
    volumes:
      - dbdata:/var/lib/postgresql/data

  api:
    build: .
    ports:
      - "8080:8080"
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://db:5432/mydb
      SPRING_DATASOURCE_USERNAME: myuser
      SPRING_DATASOURCE_PASSWORD: mypass
    depends_on:
      - db

volumes:
  dbdata:
```

---

### 5. Commandes Docker Compose

**Lancer les services (en arrière-plan) :**

```
docker compose up -d
```

**Afficher les logs :**

```
docker compose logs -f
```

**Arrêter les services :**

```
docker compose down
```

**Rebuild + restart complet :**

```
docker compose up --build -d
```

---

### 6. Variables d’environnement

Créer un fichier `.env` :

```
POSTGRES_USER=myuser
POSTGRES_PASSWORD=mypass
POSTGRES_DB=mydb
```

Réutilisation dans `docker-compose.yml` :

```
environment:
  - POSTGRES_USER=${POSTGRES_USER}
```

---

## FICHES TP CORRIGÉES – MODULE 5 – DOCKER COMPOSE

---

### TP 1 – Créer un `docker-compose.yml` avec NGINX + PHP

**Objectif :** Lancer un environnement web complet avec NGINX et PHP-FPM

**Arborescence :**

```
web/
├── index.php
├── docker-compose.yml
```

**index.php :**

```php
<?php echo "Bonjour depuis PHP via NGINX"; ?>
```

**docker-compose.yml :**

```yaml
version: '3.8'

services:
  nginx:
    image: nginx
    ports:
      - "8080:80"
    volumes:
      - ./index.php:/usr/share/nginx/html/index.php
    depends_on:
      - php

  php:
    image: php:8.2-fpm
```

**Commandes :**

```
cd web
docker compose up -d
```

**Test :**

```
curl http://localhost:8080/index.php
```

**Résultat attendu :**

* Réponse : `Bonjour depuis PHP via NGINX`

---

### TP 2 – API Java + PostgreSQL avec Docker Compose

**Objectif :** Lancer une application Java Spring Boot avec sa base de données

**Structure :**

* `Dockerfile` (voir module 3)
* `docker-compose.yml` (voir exemple plus haut)
* `.env`

**Commandes :**

```
docker compose up --build -d
```

**Test :**

* L’API doit être accessible sur [http://localhost:8080](http://localhost:8080)
* La base PostgreSQL est accessible via le conteneur `db`

**Résultat attendu :**

* L’application démarre et se connecte à la base
* Aucun crash dans `docker compose logs`

---

### TP 3 – Stack WordPress + MySQL

**Objectif :** Déployer un CMS complet en local

**docker-compose.yml :**

```yaml
version: '3.8'

services:
  wordpress:
    image: wordpress
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wppass
      WORDPRESS_DB_NAME: wpdb
    depends_on:
      - db

  db:
    image: mysql:5.7
    environment:
      MYSQL_DATABASE: wpdb
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: wppass
      MYSQL_ROOT_PASSWORD: rootpass
    volumes:
      - dbdata:/var/lib/mysql

volumes:
  dbdata:
```

**Commandes :**

```
docker compose up -d
```

**Test :**
Naviguer sur [http://localhost:8080](http://localhost:8080)

**Résultat attendu :**

* Interface d’installation de WordPress fonctionnelle
