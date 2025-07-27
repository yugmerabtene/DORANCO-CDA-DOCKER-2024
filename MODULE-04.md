**MODULE 4 – RÉSEAUX ET VOLUMES DOCKER**

---

### 1. Objectifs pédagogiques

À l’issue de ce module, l’apprenant sera capable de :

* Comprendre et manipuler les différents types de réseaux Docker
* Configurer la communication entre conteneurs
* Gérer des volumes pour la persistance des données
* Utiliser les volumes dans les contextes de développement et production

---

### 2. Les réseaux Docker

#### 2.1. Types de réseaux

| Type de réseau | Description                                         |
| -------------- | --------------------------------------------------- |
| **bridge**     | Réseau par défaut des conteneurs, en NAT            |
| **host**       | Partage la pile réseau de l’hôte (Linux uniquement) |
| **none**       | Pas de connectivité réseau                          |
| **overlay**    | Réseau multi-hôte avec Docker Swarm                 |

#### 2.2. Réseau par défaut (`bridge`)

Créer deux conteneurs sur le même réseau permet la communication par nom.

---

### 3. Commandes de gestion réseau

**Lister les réseaux :**

```
docker network ls
```

**Créer un réseau personnalisé :**

```
docker network create monreseau
```

**Supprimer un réseau :**

```
docker network rm monreseau
```

**Inspecter un réseau :**

```
docker network inspect monreseau
```

**Lancer un conteneur sur un réseau spécifique :**

```
docker run -d --name conteneur1 --network monreseau nginx
```

**Connecter un conteneur à un autre réseau :**

```
docker network connect monreseau conteneur2
```

---

### 4. Communication inter-conteneurs

Exemple :

* `conteneur1` contient une base de données PostgreSQL
* `conteneur2` est une API qui s’y connecte via l’adresse `conteneur1:5432`

Condition : les deux conteneurs doivent être sur le même réseau.

---

### 5. Les volumes Docker

#### 5.1. Types de volumes

| Type          | Description                             |
| ------------- | --------------------------------------- |
| **anonymous** | Crée automatiquement un volume sans nom |
| **named**     | Crée un volume identifiable             |
| **bind**      | Monte un dossier local de l’hôte        |

#### 5.2. Avantages des volumes :

* Persistance des données
* Partage entre conteneurs
* Backups/restaurations simplifiées

---

### 6. Commandes de gestion des volumes

**Lister les volumes :**

```
docker volume ls
```

**Créer un volume :**

```
docker volume create monvolume
```

**Supprimer un volume :**

```
docker volume rm monvolume
```

**Inspecter un volume :**

```
docker volume inspect monvolume
```

**Lancer un conteneur avec un volume :**

```
docker run -d -v monvolume:/chemin/interne nginx
```

**Monter un dossier local (bind mount) :**

* **Linux/macOS** :

```
docker run -d -v $(pwd)/site:/usr/share/nginx/html nginx
```

* **Windows PowerShell** :

```
docker run -d -v ${PWD}/site:/usr/share/nginx/html nginx
```

---

## FICHES TP CORRIGÉES – MODULE 4 – RÉSEAUX ET VOLUMES

---

### TP 1 – Créer deux conteneurs sur un réseau personnalisé

**Objectif :** Vérifier la communication entre deux conteneurs

**Étapes :**

1. Créer un réseau :

```
docker network create appnet
```

2. Lancer un serveur NGINX :

```
docker run -d --name web --network appnet nginx
```

3. Lancer un conteneur Alpine :

```
docker run -it --rm --network appnet alpine sh
```

4. Dans Alpine, installer `curl` puis tester :

```
apk add --no-cache curl
curl http://web
```

**Résultat attendu :**

* Réponse HTTP 200 ou contenu de la page NGINX

**Corrigé :**

* Le nom `web` est résolu via DNS interne Docker (grâce au réseau personnalisé)

---

### TP 2 – Partager un volume nommé entre deux conteneurs

**Objectif :** Créer un fichier depuis un conteneur, le lire depuis un autre

**Commandes :**

1. Créer le volume :

```
docker volume create data_shared
```

2. Lancer premier conteneur :

```
docker run -it --rm -v data_shared:/data ubuntu bash
```

Dans le conteneur :

```
echo "Bonjour depuis le volume" > /data/message.txt
exit
```

3. Lancer second conteneur :

```
docker run -it --rm -v data_shared:/data alpine sh
```

Dans le conteneur :

```
cat /data/message.txt
```

**Résultat attendu :**

* Le fichier est visible depuis les deux conteneurs

---

### TP 3 – Monter un dossier local dans un conteneur (bind mount)

**Objectif :** Utiliser des fichiers locaux dans un conteneur

1. Créer un dossier `site` :

* Linux/macOS :

```
mkdir site && echo "<h1>Bonjour</h1>" > site/index.html
```

* Windows PowerShell :

```
mkdir site; echo "<h1>Bonjour</h1>" > site\index.html
```

2. Lancer NGINX avec montage :

* Linux/macOS :

```
docker run -d -p 8080:80 -v $(pwd)/site:/usr/share/nginx/html nginx
```

* Windows PowerShell :

```
docker run -d -p 8080:80 -v ${PWD}/site:/usr/share/nginx/html nginx
```

3. Tester :

```
curl http://localhost:8080
```

**Résultat attendu :**

* Affichage de la page HTML personnalisée
