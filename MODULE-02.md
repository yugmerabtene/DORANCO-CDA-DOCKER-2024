**MODULE 2 – MANIPULATION DES CONTENEURS DOCKER**

---

### 1. Objectifs pédagogiques

À l’issue de ce module, l’apprenant sera capable de :

* Créer, exécuter, inspecter, arrêter et supprimer un conteneur Docker
* Gérer les modes d’exécution (interactif, détaché)
* Rediriger des ports et monter des volumes
* Travailler de manière équivalente sous Linux, Windows et macOS

---

### 2. Installation Docker (si non encore installé)

#### 2.1. Linux (Ubuntu/Debian)

```
sudo apt update
sudo apt install ca-certificates curl gnupg lsb-release
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
```

Déconnexion-reconnexion nécessaire.

#### 2.2. Windows 10/11 Pro avec WSL2

1. Activer WSL2 et Virtual Machine Platform :

```
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

2. Installer une distribution Ubuntu depuis le Microsoft Store

3. Télécharger et installer **Docker Desktop pour Windows** :
   [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)

4. Dans Docker Desktop : activer l’intégration WSL2

5. Vérifier dans Ubuntu (terminal WSL) :

```
docker --version
```

#### 2.3. macOS (Intel ou Apple Silicon)

1. Télécharger Docker Desktop pour macOS :
   [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)

2. Lancer l’installation (fichier `.dmg`)

3. Ajouter Docker à Applications et l’exécuter

4. Terminal macOS :

```
docker --version
```

---

### 3. Commandes de base multiplateformes

Ces commandes sont **identiques sur Linux, Windows (WSL/PowerShell) et macOS**, à condition que Docker soit bien installé.

#### Lancer un conteneur (mode simple)

```
docker run alpine echo "Bonjour Docker"
```

#### Mode interactif (shell)

```
docker run -it ubuntu bash
```

#### Mode détaché (en arrière-plan)

```
docker run -d nginx
```

#### Liste des conteneurs

* En cours :

```
docker ps
```

* Tous (y compris arrêtés) :

```
docker ps -a
```

#### Stop / Start / Suppression

```
docker stop nom_ou_id
docker start nom_ou_id
docker rm nom_ou_id
```

#### Suppression des conteneurs arrêtés

```
docker container prune
```

---

### 4. Accès au shell d’un conteneur

```
docker exec -it nom_ou_id bash
```

---

### 5. Logs d’un conteneur

```
docker logs nom_ou_id
```

---

### 6. Redirection de ports

```
docker run -d -p 8080:80 nginx
```

Cela expose le port 80 du conteneur sur le port 8080 de la machine hôte.

---

### 7. Montage de volumes

**Volume local → conteneur :**

```
docker run -v /chemin/local:/chemin/conteneur image
```

**Exemples :**

* **Linux/macOS :**

```
docker run -v $(pwd)/site:/usr/share/nginx/html nginx
```

* **Windows PowerShell :**

```
docker run -v ${PWD}/site:/usr/share/nginx/html nginx
```

---

## FICHES TP CORRIGÉES – MULTIPLATEFORME

---

**TP 1 – Lancer un conteneur interactif**

**Objectif :** Interagir avec un shell dans un conteneur

**Commandes (identiques partout) :**

```
docker run -it ubuntu bash
```

**Dans le conteneur :**

```
apt update && apt install -y curl
curl https://example.com
```

**Sortie :**

```
exit
```

**Résultat attendu :**

* Shell Ubuntu
* Accès réseau
* Sortie propre

---

**TP 2 – Mode détaché + test web**

**Objectif :** Lancer un conteneur NGINX et y accéder

**Commande :**

```
docker run -d -p 8080:80 --name webserver nginx
```

**Test :**

* Navigateur : [http://localhost:8080](http://localhost:8080)
* Ou `curl` :

```
curl http://localhost:8080
```

**Résultat attendu :**

* Page HTML NGINX affichée

---

**TP 3 – Exécuter une commande dans un conteneur**

```
docker exec -it webserver bash
ls /etc/nginx
```

**Résultat attendu :**

* Conteneur accessible
* Arborescence NGINX visible

---

**TP 4 – Logs**

```
docker logs webserver
curl http://localhost:8080
docker logs webserver
```

**Résultat attendu :**

* Traces des requêtes HTTP visibles
