**MODULE 6 – SÉCURITÉ DOCKER**

---

### 1. Objectifs pédagogiques

À l’issue de ce module, l’apprenant sera capable de :

* Appliquer les bonnes pratiques de sécurité dans Docker
* Utiliser des conteneurs non-root
* Scanner une image Docker à la recherche de vulnérabilités
* Protéger les secrets et renforcer l’isolation des conteneurs

---

### 2. Principes de sécurité essentiels

#### 2.1. Exécuter les conteneurs sans privilèges root

Par défaut, les conteneurs utilisent l’utilisateur `root`. Il est recommandé d’utiliser un utilisateur non privilégié dans vos images :

**Exemple dans un `Dockerfile` :**

```
RUN adduser --disabled-password --gecos '' appuser
USER appuser
```

---

#### 2.2. Moindre privilège

* Ne jamais monter `/var/run/docker.sock` dans un conteneur
* Limiter les capacités du noyau via `--cap-drop all` et `--cap-add` au besoin
* Ne pas utiliser `--privileged` sauf cas exceptionnels

---

### 3. Scanners de vulnérabilités

#### 3.1. `docker scan` (Snyk)

**Linux / macOS / WSL :**

```
docker scan monimage
```

#### 3.2. Trivy (plus complet)

**Installation sous Linux/macOS :**

```
sudo apt install wget
wget https://github.com/aquasecurity/trivy/releases/latest/download/trivy_0.50.0_Linux-64bit.deb
sudo dpkg -i trivy_0.50.0_Linux-64bit.deb
```

**Windows (WSL)** :
Même commande ou installation via binaire `.exe` depuis GitHub

**Scan d'une image :**

```
trivy image monimage
```

---

### 4. Réduction de la surface d’attaque

* Privilégier les images de base minimalistes (`alpine`, `slim`)
* Supprimer les fichiers temporaires, caches, et outils inutiles
* Activer AppArmor ou SELinux (Linux uniquement)
* Restreindre les ports exposés, éviter `EXPOSE 22`

---

### 5. Sécurisation des secrets

**Ne pas :**

* Versionner vos mots de passe dans un `Dockerfile`
* Utiliser `ENV` pour injecter des secrets

**Solutions :**

* Passer les secrets en variables d’environnement au runtime :

```
docker run -e DB_PASSWORD=monpass ...
```

* Utiliser `docker secret` (avec Swarm), ou un coffre-fort externe (HashiCorp Vault, AWS Secrets Manager)

---

### 6. Isolation système : AppArmor, SELinux, Seccomp

* **AppArmor** et **SELinux** permettent de restreindre ce que fait un conteneur (Linux uniquement)
* Docker applique un profil **seccomp** par défaut

Commandes :

```
docker info | grep seccomp
docker run --security-opt seccomp=unconfined alpine
```

---

### 7. Journalisation et audit

* Docker logge toutes les actions par défaut (`/var/log/docker.log` ou journald)
* Intégration possible avec syslog, ELK, Wazuh, etc.

---

## FICHES TP CORRIGÉES – MODULE 6 – SÉCURITÉ DOCKER

---

### TP 1 – Créer une image Docker non-root

**Objectif :** Construire une image sécurisée sans utilisateur root

**Dockerfile :**

```Dockerfile
FROM node:20-alpine
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser
WORKDIR /home/appuser
COPY app.js .
ENTRYPOINT ["node", "app.js"]
```

**Commandes multiplateformes :**

```
docker build -t app-secure .
docker run app-secure
```

**Résultat attendu :**

* L’application tourne avec un utilisateur non-root

**Vérification :**

```
docker exec -it <container_id> whoami
```

---

### TP 2 – Scanner une image avec Trivy

**Objectif :** Détecter les vulnérabilités dans une image

1. Télécharger ou construire une image :

```
docker pull nginx
```

2. Scanner :

```
trivy image nginx
```

**Résultat attendu :**

* Liste des vulnérabilités CVE avec niveau (HIGH, CRITICAL, etc.)

**Corrigé :**

* Vérifier la version de base
* Penser à utiliser `nginx:alpine` pour minimiser les risques

---

### TP 3 – Restreindre les capacités d’un conteneur

**Objectif :** Lancer un conteneur avec le minimum de privilèges

**Commande :**

```
docker run --rm -it --cap-drop ALL --security-opt no-new-privileges nginx
```

**Résultat attendu :**

* Le conteneur tourne sans capacités superflues

**Vérification dans le conteneur :**

```
cat /proc/1/status | grep CapEff
```

---

### TP 4 – Injection sécurisée de secrets

**Objectif :** Passer une variable secrète sans l’exposer dans le Dockerfile

**Commande :**

```
docker run -e DB_PASSWORD=supersecret alpine env
```

**Résultat attendu :**

* La variable `DB_PASSWORD` est présente dans l’environnement

**Corrigé :**

* Ne pas stocker cette info dans le Dockerfile
* Supprimer le conteneur après usage si critique
